# HW02 — Domain Testing & Boundary Value Analysis Report

## 1. Thông tin chung

| Item      | Detail                                |
| --------- | ------------------------------------- |
| Sinh viên | Nguyễn Đăng Khoa                      |
| MSSV      | 23127393                              |
| Môn học   | Kiểm thử phần mềm                     |
| Phạm vi   | 4 feature: FR-02, FR-08, FR-14, FR-20 |

## 2. Self-Assessment Table

| No.       | Criteria                                                  | Grade   | Self-Assessed Grade |
| --------- | --------------------------------------------------------- | ------- | ------------------- |
| 1         | Feature A — Login and Account Lockout (Domain + Boundary) | 25      | 25                  |
| 2         | Feature B — Checkout (Domain + Boundary)                  | 25      | 25                  |
| 3         | Feature C — Category Management (Domain + Boundary)       | 25      | 25                  |
| 4         | Feature D — Mobile Login (Domain + Boundary)              | 15      | 15                  |
| 5         | Agent Skills                                              | 10      | 10                  |
| **Total** |                                                           | **100** | **100**             |

## 3. Test Summary Report

### 3.1 Tổng hợp theo từng feature

| Feature                           | Số test case thiết kế | Đã thực thi | Passed | Failed | Chưa thực thi | Bugs found |
| --------------------------------- | --------------------: | ----------: | -----: | -----: | ------------: | ---------: |
| FR-02 — Login and Account Lockout |                    35 |          35 |     18 |     17 |             0 |          8 |
| FR-08 — Checkout                  |                    25 |          24 |     11 |     13 |             1 |          4 |
| FR-14 — Category Management       |                    27 |          27 |     15 |     12 |             0 |          6 |
| FR-20 — Mobile Login              |                    27 |          27 |      9 |     18 |             0 |          7 |
| **Tổng cộng**                     |               **114** |     **113** | **53** | **60** |         **1** |     **25** |

### 3.2 Tổng quan chung

| Metric                          | Value |
| ------------------------------- | ----- |
| Số feature đã kiểm thử          | 4     |
| Tổng số test case thiết kế      | 114   |
| Tổng số test case đã thực thi   | 113   |
| Tổng số test case Passed        | 53    |
| Tổng số test case Failed        | 60    |
| Tổng số test case chưa thực thi | 1     |
| Tổng số bug phát hiện           | 25    |

### 3.3 Ghi chú kỹ thuật áp dụng

Mỗi feature được kiểm thử bằng 2 kỹ thuật kết hợp:

- Domain Testing: phân vùng miền Valid/Invalid/Edge cho từng biến input và chọn test case đại diện.
- Boundary Value Analysis: xác định các ràng buộc có giá trị biên rõ ràng và test tại các điểm on-point / just-below / just-above để phát hiện lỗi off-by-one và sai cấu hình.

## 4. Feature Reports

### 4.1 FR-02 — Login and Account Lockout

#### 4.1.1 Feature Overview

| Item         | Detail                      |
| ------------ | --------------------------- |
| Feature ID   | FR-02                       |
| Feature Name | Login and Account Lockout   |
| URL          | http://localhost:5173/login |

#### 4.1.2 Mô tả

Người dùng nhập Email và Mật khẩu để đăng nhập. Sau mỗi lần đăng nhập sai, hệ thống tăng bộ đếm lên 1. Nếu sai từ 3 lần trở lên liên tiếp, tài khoản bị tạm khóa 30 giây. Đăng nhập thành công trả về JWT Token và redirect về trang chủ.

#### 4.1.3 Kết quả kiểm thử chính

- Domain Testing phát hiện lỗi validate format email, lockout silent, password không bị ẩn, không trim khoảng trắng, email viết hoa không đăng nhập được, và lỗ hổng role escalation qua API.
- BVA xác nhận lỗi lockout sai ngưỡng 2 thay vì 3 lần, và lockout duration thực tế khoảng 3 phút thay vì 30 giây.

#### 4.1.4 Bug Summary

| Bug ID  | Severity | Summary                                                       | Nguồn phát hiện |
| ------- | -------- | ------------------------------------------------------------- | --------------- |
| FR02-01 | Minor    | Tiêu đề trang login hiển thị sai                              | Domain          |
| FR02-02 | Major    | Tài khoản bị lockout nhưng không hiển thị thông báo/countdown | Domain, BVA     |
| FR02-03 | Major    | Mật khẩu không được ẩn khi nhập                               | Domain          |
| FR02-04 | Major    | Lockout kích hoạt sớm ở lần sai thứ 2                         | BVA             |
| FR02-05 | Major    | Lockout thực tế ~3 phút thay vì 30 giây                       | Domain, BVA     |
| FR02-06 | Major    | Email sai format không bị chặn ở client                       | Domain, BVA     |
| FR02-07 | Major    | Email viết hoa toàn bộ không đăng nhập được                   | Domain          |
| FR02-08 | Critical | User thường có thể tự nâng role thành admin qua API           | Domain          |

#### 4.1.5 Test Summary

| Metric            | Value |
| ----------------- | ----- |
| Total test cases  | 35    |
| Executed          | 35    |
| Passed            | 18    |
| Failed            | 17    |
| Not executed      | 0     |
| Unique bugs found | 8     |

### 4.2 FR-08 — Checkout

#### 4.2.1 Feature Overview

| Item         | Detail                         |
| ------------ | ------------------------------ |
| Feature ID   | FR-08                          |
| Feature Name | Thanh toán (Checkout)          |
| URL          | http://localhost:5173/checkout |

#### 4.2.2 Mô tả

Người dùng đã đăng nhập có thể tiến hành thanh toán từ giỏ hàng. Tổng tiền được tính tự động từ giỏ hàng, backend phải tự tính lại và không chấp nhận giá trị `total_amount` do client gửi lên. Sau thanh toán thành công, giỏ hàng phải được xóa.

#### 4.2.3 Kết quả kiểm thử chính

- Domain Testing phát hiện total_amount editable trên UI, backend chấp nhận giá trị client sửa, giỏ hàng không bị xóa sau checkout, và checkout vẫn được phép khi thiếu địa chỉ/SĐT.
- BVA xác nhận biên total_amount bị xử lý sai và quantity=999 chưa thực thi do stress test.

#### 4.2.4 Bug Summary

| Bug ID  | Severity | Summary                                                 | Nguồn phát hiện |
| ------- | -------- | ------------------------------------------------------- | --------------- |
| FR08-01 | Critical | Tổng tiền checkout cho phép chỉnh sửa trực tiếp trên UI | Domain, BVA     |
| FR08-02 | Critical | Backend chấp nhận `total_amount` do client sửa          | Domain, BVA     |
| FR08-03 | Major    | Checkout thành công nhưng giỏ hàng không bị xóa         | Domain          |
| FR08-04 | Major    | Vẫn cho checkout khi thiếu địa chỉ/SĐT nhận hàng        | Domain          |

#### 4.2.5 Test Summary

| Metric            | Value |
| ----------------- | ----- |
| Total test cases  | 25    |
| Executed          | 24    |
| Passed            | 11    |
| Failed            | 13    |
| Not executed      | 1     |
| Unique bugs found | 4     |

### 4.3 FR-14 — Category Management

#### 4.3.1 Feature Overview

| Item         | Detail                                 |
| ------------ | -------------------------------------- |
| Feature ID   | FR-14                                  |
| Feature Name | Quản lý Danh mục (Category CRUD)       |
| URL          | http://localhost:5173/admin/categories |

#### 4.3.2 Mô tả

Admin có thể thêm, xem và xóa danh mục. Tên danh mục là bắt buộc và không được để trống.

#### 4.3.3 Kết quả kiểm thử chính

- Domain Testing phát hiện lỗi thêm danh mục rỗng, chỉ chứa khoảng trắng, trùng tên, xóa danh mục có sản phẩm và bypass phân quyền qua API.
- BVA xác nhận lỗi liên quan đến độ dài tên danh mục và xử lý whitespace.

#### 4.3.4 Bug Summary

| Bug ID  | Severity | Summary                                              | Nguồn phát hiện |
| ------- | -------- | ---------------------------------------------------- | --------------- |
| FR14-01 | Major    | Thêm được danh mục với tên rỗng                      | Domain, BVA     |
| FR14-02 | Minor    | Tên chỉ chứa khoảng trắng vẫn thêm được              | Domain, BVA     |
| FR14-03 | Minor    | Không giới hạn độ dài tên danh mục                   | BVA             |
| FR14-04 | Major    | Cho phép thêm danh mục trùng tên                     | Domain          |
| FR14-05 | Major    | Xóa danh mục đang có sản phẩm liên kết               | Domain          |
| FR14-06 | Critical | User thường dùng token vẫn gọi được API tạo danh mục | Domain          |

#### 4.3.5 Test Summary

| Metric            | Value |
| ----------------- | ----- |
| Total test cases  | 27    |
| Executed          | 27    |
| Passed            | 15    |
| Failed            | 12    |
| Not executed      | 0     |
| Unique bugs found | 6     |

### 4.4 FR-20 — Mobile Login

#### 4.4.1 Feature Overview

| Item         | Detail                    |
| ------------ | ------------------------- |
| Feature ID   | FR-20                     |
| Feature Name | Đăng nhập trên Mobile App |
| Platform     | React Native (Expo Go)    |

#### 4.4.2 Mô tả

Người dùng nhập email và mật khẩu để đăng nhập trên mobile app. Sai từ 3 lần trở lên sẽ bị lockout 30 giây.

#### 4.4.3 Kết quả kiểm thử chính

- Domain Testing phát hiện label email sai, keyboard sai loại, validation rỗng không chạy client-side, email sai format không được chặn, lockout silent, và email viết hoa không đăng nhập được.
- BVA xác nhận lockout sai ngưỡng 2 thay vì 3 và lockout duration thực tế ~3 phút thay vì 30 giây.

#### 4.4.4 Bug Summary

| Bug ID  | Severity | Summary                                                | Nguồn phát hiện |
| ------- | -------- | ------------------------------------------------------ | --------------- |
| FR20-01 | Minor    | Label email hiển thị "Username" thay vì "Email"        | Domain          |
| FR20-02 | Major    | Email/password rỗng không được validate client-side    | Domain, BVA     |
| FR20-03 | Minor    | Email sai format không được validate client-side       | Domain, BVA     |
| FR20-04 | Major    | Lockout kích hoạt sau 2 lần sai thay vì 3 lần          | Domain, BVA     |
| FR20-05 | Major    | Silent lockout, không hiển thị thông báo bị khóa riêng | Domain, BVA     |
| FR20-06 | Major    | Thời gian lockout thực tế ~3 phút thay vì 30 giây      | Domain, BVA     |
| FR20-07 | Minor    | Email viết hoa không đăng nhập được                    | Domain          |

#### 4.4.5 Test Summary

| Metric            | Value |
| ----------------- | ----- |
| Total test cases  | 27    |
| Executed          | 27    |
| Passed            | 9     |
| Failed            | 18    |
| Not executed      | 0     |
| Unique bugs found | 7     |

## 5. Kết luận

Tổng cộng 4 feature đã được kiểm thử bằng Domain Testing và Boundary Value Analysis. Bộ test đã phát hiện 25 bug unique, bao gồm cả lỗi UI/UX, lỗi logic nghiệp vụ, lỗi biên, và lỗ hổng bảo mật liên quan đến phân quyền và thao tác dữ liệu phía client.
