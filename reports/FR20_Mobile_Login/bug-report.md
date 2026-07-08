# Test Report — FR20: Mobile Login

## 1. Bug Reports

### BUG-01: Label field email hiển thị "Username" thay vì "Email"

| Field             | Detail                                                                                                                                                                              |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bug ID**        | BUG-01                                                                                                                                                                              |
| **Phát hiện tại** | TC-14 (Domain Testing)                                                                                                                                                              |
| **Severity**      | Minor                                                                                                                                                                               |
| **Priority**      | Low                                                                                                                                                                                 |
| **Summary**       | Label của field email hiển thị "Username" thay vì "Email", đồng thời bàn phím hiện lên khi focus vào field này là bàn phím loại thường thay vì loại email (không có phím @ nổi bật) |
| **Expected**      | Label hiển thị "Email"; bàn phím hiện lên là loại email                                                                                                                             |
| **Actual**        | Label hiển thị "Username"; bàn phím hiện lên là loại thường                                                                                                                         |
| **Screenshot**    | `screenshots/bug-01-wrong-label.png`                                                                                                                                                |

### BUG-02: Không validate khi email/password rỗng

| Field             | Detail                                                                                                                                                                                                                                             |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bug ID**        | BUG-02                                                                                                                                                                                                                                             |
| **Phát hiện tại** | TC-02, TC-03, TC-04 (Domain Testing) + BVA-08, BVA-10 (BVA) — cùng một lỗi                                                                                                                                                                         |
| **Severity**      | Major                                                                                                                                                                                                                                              |
| **Priority**      | High                                                                                                                                                                                                                                               |
| **Summary**       | Khi email hoặc password rỗng, app không báo lỗi bắt buộc nhập mà vẫn gửi request lên server và hiển thị "Đăng nhập thất bại" — gây nhầm lẫn cho người dùng. Đặc biệt: khi email chính xác và password rỗng, hệ thống vẫn tính vào `login_attempts` |
| **Expected**      | Báo lỗi "Vui lòng nhập email/mật khẩu" ngay trên client, không gửi request lên server                                                                                                                                                              |
| **Actual**        | Gửi request lên server, trả về "Đăng nhập thất bại", tính vào `login_attempts` khi email đúng và password rỗng                                                                                                                                     |
| **Screenshot**    | `screenshots/bug-02-password-empty-submit.png`                                                                                                                                                                                                     |

### BUG-03: Không validate format email

| Field             | Detail                                                                                                                                  |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| **Bug ID**        | BUG-03                                                                                                                                  |
| **Phát hiện tại** | TC-05 (Domain Testing) + BVA-09 (BVA) — cùng một lỗi                                                                                    |
| **Severity**      | Minor                                                                                                                                   |
| **Priority**      | Medium                                                                                                                                  |
| **Summary**       | Email sai format (thiếu @, hoặc chỉ có 1 ký tự) không bị báo lỗi định dạng mà vẫn gửi request lên server và trả về "Đăng nhập thất bại" |
| **Expected**      | Báo lỗi "Email không đúng định dạng" ngay trên client                                                                                   |
| **Actual**        | Cho phép submit, chỉ hiển thị "Đăng nhập thất bại"                                                                                      |
| **Screenshot**    | `screenshots/bug-03-no-warning-email-wrong-format.png`                                                                                  |

### BUG-04: Lockout xảy ra sau 2 lần sai thay vì 3 lần

| Field             | Detail                                                                                                        |
| ----------------- | ------------------------------------------------------------------------------------------------------------- |
| **Bug ID**        | BUG-04                                                                                                        |
| **Phát hiện tại** | TC-08 (Domain Testing) + BVA-02 (BVA) — cùng một lỗi                                                          |
| **Severity**      | Major                                                                                                         |
| **Priority**      | High                                                                                                          |
| **Summary**       | Tài khoản bị khóa sau chỉ 2 lần đăng nhập sai — vi phạm spec quy định lockout sau đúng 3 lần sai (off-by-one) |
| **Expected**      | Lockout kích hoạt sau đúng 3 lần sai (on-point)                                                               |
| **Actual**        | Lockout kích hoạt sau 2 lần sai (dưới ngưỡng biên)                                                            |

### BUG-05: Silent lockout — không hiển thị thông báo bị khóa

| Field             | Detail                                                                                                                                                                                                            |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bug ID**        | BUG-05                                                                                                                                                                                                            |
| **Phát hiện tại** | TC-09, TC-10 (Domain Testing) + BVA-03 (BVA, on-point attempts=3) — cùng một lỗi                                                                                                                                  |
| **Severity**      | Major                                                                                                                                                                                                             |
| **Priority**      | High                                                                                                                                                                                                              |
| **Summary**       | Khi tài khoản bị lockout (kể cả đúng tại ngưỡng 3 lần sai), app hiển thị cùng message "Đăng nhập thất bại" như lỗi sai password — người dùng không biết lý do tại sao không đăng nhập được dù nhập đúng thông tin |
| **Expected**      | Hiển thị "Tài khoản bị tạm khóa, vui lòng thử lại sau X giây"                                                                                                                                                     |
| **Actual**        | Hiển thị "Đăng nhập thất bại. Vui lòng kiểm tra lại" cho mọi loại lỗi, không phân biệt sai password hay đang bị khóa                                                                                              |

### BUG-06: Thời gian lockout thực tế 3 phút thay vì 30 giây

| Field             | Detail                                                                                                                                                                                           |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Bug ID**        | BUG-06                                                                                                                                                                                           |
| **Phát hiện tại** | TC-11 (Domain Testing) + BVA-05, BVA-06, BVA-07 (BVA — test tại các mốc 29s/30s/31s) — cùng một lỗi                                                                                              |
| **Severity**      | Major                                                                                                                                                                                            |
| **Priority**      | High                                                                                                                                                                                             |
| **Summary**       | Spec quy định lockout 30 giây nhưng thực tế người dùng phải chờ khoảng 3 phút mới mở khóa được, kể cả tại đúng mốc 30 giây hoặc sau 31 giây; hệ thống cũng không thông báo thời gian chờ còn lại |
| **Expected**      | Mở khóa và đăng nhập thành công ngay sau đúng 30 giây                                                                                                                                            |
| **Actual**        | Sau 29s, 30s, 31s tài khoản vẫn bị khóa; phải chờ khoảng 3 phút mới đăng nhập lại được                                                                                                           |

### BUG-07: Email chữ HOA không đăng nhập được

| Field             | Detail                                                                                                                   |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------ |
| **Bug ID**        | BUG-07                                                                                                                   |
| **Phát hiện tại** | TC-12 (Domain Testing)                                                                                                   |
| **Severity**      | Minor                                                                                                                    |
| **Priority**      | Medium                                                                                                                   |
| **Summary**       | Nhập email chữ HOA (`USER@GMAIL.COM`) thất bại dù email đúng — hệ thống phân biệt hoa/thường với email, không đúng chuẩn |
| **Expected**      | Đăng nhập thành công (email nên được xử lý case-insensitive theo chuẩn RFC)                                              |
| **Actual**        | Đăng nhập thất bại                                                                                                       |

---

## 2. AI Gap Analysis

| Gap                                | Mô tả                                                                 | Lý do AI bỏ sót                                                                                                                           |
| ---------------------------------- | --------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| BUG-01 (label/keyboard sai)        | Label sai, bàn phím hiện lên là loại thường thay vì loại email        | AI không trải nghiệm giao diện mobile thực tế nên không thể tự nhận biết các chi tiết UI như label hay loại bàn phím hiển thị             |
| BUG-02 (empty field validation)    | Không validate email/password rỗng phía client                        | AI giả định validation được implement đúng theo spec, không kiểm tra hành vi thực tế của app                                              |
| BUG-03 (email format validation)   | Không validate format email phía client                               | AI tin tưởng spec 100%, không tự kiểm tra hành vi thực tế khi nhập dữ liệu sai định dạng                                                  |
| BUG-04 (off-by-one lockout)        | Lockout kích hoạt ở 2 thay vì 3 lần sai                               | Domain testing thông thường không đủ chi tiết ở từng mốc biên; chỉ khi test chính xác tại các điểm on-point/just-below mới phát hiện được |
| BUG-05 (silent lockout)            | Không phân biệt thông báo giữa sai password và bị khóa                | AI tin tưởng spec ghi "có thông báo lockout" nên assume là đúng, không kiểm tra UX thực tế                                                |
| BUG-06 (thời gian lockout thực tế) | Test chính xác tại 29s/30s/31s cho thấy lockout kéo dài khoảng 3 phút | AI không thể đo thời gian thực thi của hệ thống trên thiết bị mobile — cần con người test thủ công theo đồng hồ                           |
| BUG-07 (case-insensitive email)    | Email viết hoa bị coi là không hợp lệ                                 | Logic nghiệp vụ ẩn, AI không suy luận được nếu không có spec rõ ràng và không có kết quả test thực tế xác nhận                            |

---

## 3. Test Summary

| Metric            | Value                   |
| ----------------- | ----------------------- |
| Total test cases  | 27 (15 Domain + 12 BVA) |
| Executed          | 27                      |
| Passed            | 9                       |
| Failed            | 18                      |
| Not executed      | 0                       |
| Unique bugs found | 7                       |
