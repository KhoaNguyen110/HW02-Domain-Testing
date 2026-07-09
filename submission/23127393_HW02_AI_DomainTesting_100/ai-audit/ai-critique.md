# AI Critique - HW02 Domain Testing

## Phê bình AI trong quá trình thực hiện HW02

Trong quá trình thực hiện bài tập HW02, tôi đã sử dụng Claude (claude.ai) để hỗ trợ thiết kế test cases theo kỹ thuật Domain Testing và Boundary Value Analysis (BVA) cho ba feature FR-02 (Login and Account Lockout), FR-08 (Checkout), và FR-14 (Category Management) của ứng dụng EShop. AI khá hữu ích trong việc phân tích và thiết kế test case khá chuẩn, nhưng qua từng feature, tôi nhận ra nhiều điểm giới hạn khác nhau của AI mà nếu không tự tay kiểm thử thì sẽ không thể phát hiện ra.

### 1. AI không thể phát hiện lỗi UI/UX vì không chạy ứng dụng thực tế

Ở FR-02, AI không phát hiện được BUG-01 (tiêu đề trang Login hiển thị "Đăng ký" thay vì "Đăng nhập") và BUG-06 (password hiển thị dạng plaintext thay vì ẩn bằng ký tự đặc biệt). Những lỗi này chỉ được phát hiện khi tôi tự mở ứng dụng và quan sát trực tiếp. AI luôn giả định giao diện được implement đúng theo spec, trong khi thực tế lại khác hoàn toàn. Tương tự ở FR-08, AI không thể tự nhận ra rằng field tổng tiền trên UI là editable thay vì readonly.

### 2. AI tin tưởng spec một cách tuyệt đối, không đặt câu hỏi với implementation

Khi spec FR-02 ghi "lockout sau 30 giây", AI thiết kế test case dựa trên đúng con số đó mà không đặt câu hỏi liệu implementation có tuân thủ hay không. Thực tế thời gian lockout đo được là khoảng 3 phút - một sai lệch lớn mà chỉ human test thủ công (bấm giờ thực tế) mới phát hiện được. Ở FR-14, AI cũng mặc định rằng validate "tên danh mục bắt buộc nhập" đã được implement đúng, nên không tự nghi ngờ khi test case tên rỗng và tên chỉ chứa khoảng trắng đều được hệ thống chấp nhận.

### 3. Domain Testing chỉ phát hiện "có sai lệch", còn BVA mới khoanh vùng chính xác được lỗi off-by-one

Đây là bài học quan trọng nhất khi so sánh kết quả giữa hai kỹ thuật trên cùng FR-02. Domain Testing (test tại các mốc 0, 1, 2, 3+ lần sai một cách rời rạc) chỉ dừng ở mức phát hiện "tài khoản bị khóa sớm hơn dự kiến". Nhưng chỉ khi áp dụng BVA - thiết kế test case chính xác tại từng điểm biên liên tiếp (2 lần sai = just-below-boundary, 3 lần sai = on-point boundary, 4 lần sai = just-above-boundary) - mới xác nhận chắc chắn được đây là lỗi **off-by-one**: hệ thống khóa tài khoản ngay sau **2 lần sai** thay vì đúng 3 lần như spec quy định. Đây là minh chứng rõ nhất cho việc BVA hiệu quả hơn Domain Testing thuần túy trong việc chẩn đoán chính xác bản chất kỹ thuật của lỗi tại các ngưỡng số học/thời gian.

### 4. AI không thể tự thao tác các kỹ thuật kiểm thử ngoài phạm vi UI tuần tự

Ở FR-08, AI ghi nhận nghi vấn rằng backend có thể không tính lại `total_amount` khi nhận request checkout, nhưng chỉ dừng ở mức "cần verify" trong báo cáo Domain Testing, vì AI không thể tự mở DevTools, intercept network request, và sửa giá trị gửi lên server. Chỉ khi tôi tự thực hiện thao tác này ở bước BVA thì mới xác nhận được đây là lỗ hổng bảo mật thật sự (backend chấp nhận mọi giá trị total_amount, kể cả 0 hoặc số âm). Tương tự ở FR-14, AI không tự gọi trực tiếp API với token của user thường để kiểm tra role bypass - việc này cũng cần tôi thực hiện thủ công mới phát hiện ra lỗ hổng SEC-03 (user thường vẫn tạo được danh mục qua API). Đây là một giới hạn khác biệt với mục (1): không phải AI "không thấy" vì thiếu giao diện, mà vì kỹ thuật kiểm thử (network interception, gọi API trực tiếp) nằm ngoài khả năng thao tác của AI trong vai trò phân tích black-box thuần túy.

### 5. AI không chủ động nghĩ đến security test nếu không được nhắc trong prompt

AI không tự đề xuất các test case như SQL injection, XSS, hay privilege escalation (SEC-03, SEC-06) trừ khi được nhắc rõ trong spec hoặc observed behavior. Điều này cho thấy AI chỉ phân tích trong phạm vi thông tin được cung cấp, không tự suy luận ra các rủi ro bảo mật tiềm ẩn nếu người ra đề không chủ động đưa vào yêu cầu.

### 6. Có những gap không phải do AI bỏ sót, mà do bản thân spec chưa đầy đủ

Một điểm cần phân biệt rõ: không phải mọi gap đều là lỗi của AI. Ví dụ ở FR-02, spec không hề định nghĩa độ dài tối thiểu/tối đa của password, cũng không làm rõ cơ chế khóa tài khoản là theo account hay theo session/IP. Trong những trường hợp này, AI không thể "tự bịa" ra một con số biên hay một hành vi cụ thể để thiết kế test case - đây là **spec gap** cần được escalate lên BA/PM để làm rõ trước, chứ không phải là điểm yếu về năng lực phân tích của AI. Việc phân biệt rạch ròi giữa "AI bỏ sót do giới hạn công cụ" và "spec chưa đủ thông tin" giúp việc đánh giá AI công bằng và chính xác hơn.

### Kết luận

Qua các feature, có thể thấy AI đóng vai trò tốt ở việc cấu trúc hóa quy trình Domain Testing/BVA, tạo bộ khung test case bao phủ đầy đủ các miền giá trị theo kỹ thuật, và tiết kiệm đáng kể thời gian soạn thảo tài liệu. Tuy nhiên, để phát hiện bug thực sự, người tester vẫn buộc phải: tự chạy app và quan sát UI trực tiếp, tự đo đạc các giá trị thời gian/số học thực tế thay vì tin vào spec, tự thực hiện các kỹ thuật ngoài phạm vi UI như DevTools/gọi API trực tiếp, và biết phân biệt giữa gap do AI giới hạn với gap do spec chưa hoàn chỉnh để đưa ra hướng xử lý phù hợp (bổ sung test thủ công hay hỏi lại BA/PM). Nói cách khác, AI là công cụ giúp tester tăng hiệu suất làm việc, nhưng thao tác kiểm thử thực tế của con người vẫn là yếu tố quyết định chất lượng cuối cùng của bộ test case.
