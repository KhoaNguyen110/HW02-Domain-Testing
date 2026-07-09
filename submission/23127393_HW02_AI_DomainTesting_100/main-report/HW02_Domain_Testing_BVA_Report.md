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

| Item         | Detail                                   |
| ------------ | ---------------------------------------- |
| Feature ID   | FR-02                                    |
| Feature Name | Login and Account Lockout                |
| URL          | http://localhost:5173/login              |
| Technique    | Domain Testing + Boundary Value Analysis |

#### 4.1.2 Mô tả

Người dùng nhập Email và Mật khẩu để đăng nhập. Sau mỗi lần đăng nhập sai, hệ thống tăng bộ đếm lên 1. Nếu sai từ 3 lần trở lên liên tiếp, tài khoản bị tạm khóa 30 giây. Đăng nhập thành công trả về JWT Token và redirect về trang chủ.

#### 4.1.3 Domain Testing — Step-by-step

##### Bước 1: Xác định biến input

| Biến           | Kiểu    | Mô tả                          |
| -------------- | ------- | ------------------------------ |
| email          | String  | Địa chỉ email đăng nhập        |
| password       | String  | Mật khẩu đăng nhập             |
| login_attempts | Integer | Số lần đăng nhập sai liên tiếp |

##### Bước 2: Phân tích và phân vùng miền

###### Biến email

| Miền | Loại    | Điều kiện                                 | Ví dụ              |
| ---- | ------- | ----------------------------------------- | ------------------ |
| D1   | Valid   | Đúng format email HTML5, tồn tại trong DB | user@example.com   |
| D2   | Invalid | Sai format, không có @                    | userexample.com    |
| D3   | Invalid | Sai format, thiếu domain                  | user@              |
| D4   | Invalid | Sai format, chỉ có @                      | @gmail.com         |
| D5   | Invalid | Đúng format nhưng không tồn tại trong DB  | notexist@gmail.com |
| D6   | Edge    | Rỗng                                      | ``                 |
| D7   | Edge    | Chỉ có khoảng trắng                       | `   `              |
| D8   | Edge    | Email chữ HOA                             | USER@GMAIL.COM     |

###### Biến password

| Miền | Loại    | Điều kiện                           | Ví dụ          |
| ---- | ------- | ----------------------------------- | -------------- |
| P1   | Valid   | Đúng password của tài khoản         | correctPass123 |
| P2   | Invalid | Sai password                        | wrongPass      |
| P3   | Edge    | Rỗng                                | ``             |
| P4   | Edge    | Có khoảng trắng đầu/cuối            | ` pass`        |
| P5   | Edge    | Password đúng nhưng khác hoa/thường | CORRECTPASS123 |

###### Biến login_attempts

| Miền | Loại         | Giá trị     | Hành vi mong đợi          |
| ---- | ------------ | ----------- | ------------------------- |
| A1   | Valid        | 0           | Không bị ảnh hưởng        |
| A2   | Valid        | 1 lần sai   | Báo lỗi, cho thử lại      |
| A3   | Valid        | 2 lần sai   | Báo lỗi, cho thử lại      |
| A4   | Boundary     | 3 lần sai   | Kích hoạt lockout 30 giây |
| A5   | Invalid      | 4+ lần sai  | Vẫn bị khóa               |
| A6   | Post-lockout | Sau 30 giây | Cho phép thử lại          |

##### Bước 3: Chọn test case đại diện

#### 4.1.4 Domain Testing — Test Case Table

| TC ID | Mô tả                                       | Email (Miền)          | Password (Miền) | Attempts    | Kết quả mong đợi                               | Kết quả thực tế                                                             | Pass/Fail |
| ----- | ------------------------------------------- | --------------------- | --------------- | ----------- | ---------------------------------------------- | --------------------------------------------------------------------------- | --------- |
| TC-01 | Đăng nhập thành công                        | D1 valid email        | P1 correct pass | A1 (0)      | Redirect về /, nhận JWT Token                  | Đăng nhập thành công, token tồn tại                                         | Pass      |
| TC-02 | Email rỗng                                  | D6 empty              | P1 correct pass | A1 (0)      | Báo bắt buộc nhập email                        | Hiển thị thông báo trường dữ liệu bắt buộc nhập                             | Pass      |
| TC-03 | Password rỗng                               | D1 valid email        | P3 empty        | A1 (0)      | Báo bắt buộc nhập password                     | Hiển thị thông báo trường dữ liệu bắt buộc nhập                             | Pass      |
| TC-04 | Cả 2 field rỗng                             | D6 empty              | P3 empty        | A1 (0)      | Báo bắt buộc nhập                              | Hiển thị thông báo trường dữ liệu bắt buộc nhập                             | Pass      |
| TC-05 | Email sai format (không có @)               | D2 userexample.com    | P1 correct pass | A1 (0)      | HTML5 validation báo sai format                | Chỉ hiển thị thông báo "Đăng nhập thất bại"                                 | Fail      |
| TC-06 | Email sai format (thiếu domain)             | D3 user@              | P1 correct pass | A1 (0)      | HTML5 validation báo sai format                | Chỉ hiển thị thông báo "Đăng nhập thất bại"                                 | Fail      |
| TC-07 | Email chỉ có @                              | D4 @gmail.com         | P1 correct pass | A1 (0)      | HTML5 validation báo sai format                | Chỉ hiển thị thông báo "Đăng nhập thất bại"                                 | Fail      |
| TC-08 | Email không tồn tại                         | D5 notexist@gmail.com | P2 wrong pass   | A1 (0)      | Báo đăng nhập thất bại                         | Hiển thị thông báo "Đăng nhập thất bại"                                     | Pass      |
| TC-09 | Sai password lần 1                          | D1 valid email        | P2 wrong pass   | A2 (1)      | Báo đăng nhập thất bại, cho thử lại            | Hiển thị thông báo "Đăng nhập thất bại", cho phép thử lại                   | Pass      |
| TC-10 | Sai password lần 2                          | D1 valid email        | P2 wrong pass   | A3 (2)      | Báo đăng nhập thất bại, cho thử lại            | Tài khoản bị khóa, không cho phép thử lại                                   | Fail      |
| TC-11 | Sai password lần 3 — trigger lockout        | D1 valid email        | P2 wrong pass   | A4 (3)      | Tài khoản bị khóa 30 giây, hiển thị thông báo  | Tài khoản bị khóa, nhưng không hiển thị thông báo đến người dùng            | Fail      |
| TC-12 | Login đúng pass khi đang lockout            | D1 valid email        | P1 correct pass | A4 (locked) | Vẫn không vào được, hiển thị thông báo bị khóa | Tài khoản vẫn bị khóa, không hiển thị thông báo, không submit được          | Fail      |
| TC-13 | Login sai tiếp khi đang lockout             | D1 valid email        | P2 wrong pass   | A5 (4+)     | Vẫn bị khóa                                    | Vẫn bị khóa                                                                 | Pass      |
| TC-14 | Login đúng sau khi hết lockout (>30s)       | D1 valid email        | P1 correct pass | A6          | Đăng nhập thành công, redirect về /            | Tài khoản vẫn bị khóa, người dùng phải chờ 3 phút                           | Fail      |
| TC-15 | Password có khoảng trắng                    | D1 valid email        | P4 ` pass`      | A1 (0)      | Xác định hệ thống có trim không                | Hệ thống không tự động trim text khi có khoảng trắng                        | Fail      |
| TC-16 | Email chữ HOA                               | D8 USER@GMAIL.COM     | P1 correct pass | A1 (0)      | Đăng nhập thành công (case-insensitive)        | Đăng nhập thất bại                                                          | Fail      |
| TC-17 | Password đúng nhưng khác hoa/thường         | D1 valid email        | P5 CORRECTPASS  | A1 (0)      | Đăng nhập thất bại (case-sensitive)            | Đăng nhập thất bại                                                          | Pass      |
| TC-18 | Reset attempts sau login thành công         | D1 valid email        | P1 correct pass | A3          | Thành công, bộ đếm về 0                        | Bộ đếm reset về 0, không bị khóa ngay khi nhập sai lại                      | Pass      |
| TC-19 | Gửi request cập nhật profile với role=admin | D1 valid email        | P1 correct pass | -           | Backend bỏ qua trường role, không cho đổi role | Cập nhật và đăng nhập admin thành công bằng tài khoản user sau khi cập nhật | Fail      |

#### 4.1.5 BVA — Boundary Analysis

##### Ràng buộc

| Biến             | Ràng buộc                                            | Nguồn    |
| ---------------- | ---------------------------------------------------- | -------- |
| email            | Phải đúng format HTML5 (type="email")                | README   |
| password         | Không rỗng                                           | Observed |
| login_attempts   | Sai < 3 lần cho thử lại, sai ≥ 3 lần lockout 30 giây | README   |
| lockout_duration | Khóa đúng 30 giây                                    | README   |

##### login_attempts — Ngưỡng lockout

| Điểm biên           | Giá trị    | Tên                  | Kết quả mong đợi          |
| ------------------- | ---------- | -------------------- | ------------------------- |
| Below lower bound   | 0 lần sai  | Không có lần sai nào | Cho đăng nhập bình thường |
| In-range            | 1 lần sai  | Trong miền valid     | Báo lỗi, cho thử lại      |
| Just below boundary | 2 lần sai  | Dưới biên lockout    | Báo lỗi, vẫn cho thử lại  |
| On-point            | 3 lần sai  | Đúng ngưỡng lockout  | Kích hoạt lockout 30 giây |
| Just above boundary | 4 lần sai  | Vượt biên            | Vẫn bị khóa               |
| Far above boundary  | 10 lần sai | Rất cao              | Vẫn bị khóa               |

##### lockout_duration — Thời gian khóa 30 giây

| Điểm biên           | Giá trị             | Kết quả mong đợi                  |
| ------------------- | ------------------- | --------------------------------- |
| Just below boundary | 29 giây sau lockout | Vẫn bị khóa, không đăng nhập được |
| On-point            | 30 giây sau lockout | Tài khoản được mở khóa            |
| Just above boundary | 31 giây sau lockout | Đăng nhập thành công              |

##### email — Format HTML5

| Điểm biên                     | Giá trị        | Kết quả mong đợi     |
| ----------------------------- | -------------- | -------------------- |
| Invalid — thiếu @             | usergmail.com  | HTML5 báo sai format |
| Invalid — thiếu local part    | @gmail.com     | HTML5 báo sai format |
| Invalid — thiếu domain        | user@          | HTML5 báo sai format |
| Valid — đủ cấu trúc tối thiểu | a@b.c          | Hợp lệ về format     |
| Valid — format đầy đủ         | user@gmail.com | Hợp lệ về format     |
| Edge — rỗng                   | ``             | Báo bắt buộc nhập    |

##### password — Độ dài

| Điểm biên        | Giá trị       | Kết quả mong đợi                       |
| ---------------- | ------------- | -------------------------------------- |
| On-point rỗng    | ``            | Báo bắt buộc nhập password             |
| Just above empty | 1 ký tự `a`   | Cho phép submit (validate phía server) |
| Typical valid    | password đúng | Đăng nhập thành công                   |

#### 4.1.6 BVA — Test Case Table

| TC ID  | Mô tả                                     | Input                                  | Biên liên quan                   | Kết quả mong đợi                               | Kết quả thực tế                                           | Pass/Fail |
| ------ | ----------------------------------------- | -------------------------------------- | -------------------------------- | ---------------------------------------------- | --------------------------------------------------------- | --------- |
| BVA-01 | Không có lần sai nào, login đúng          | email đúng, pass đúng, 0 lần sai trước | Below lower bound (attempts=0)   | Đăng nhập thành công                           | Đăng nhập thành công                                      | Pass      |
| BVA-02 | Sai 1 lần                                 | email đúng, pass sai × 1               | In-range (attempts=1)            | Báo lỗi, cho thử lại                           | Thử lại và đăng nhập thành công                           | Pass      |
| BVA-03 | Sai 2 lần — dưới biên lockout             | email đúng, pass sai × 2               | Just below boundary (attempts=2) | Báo lỗi, vẫn cho thử lại                       | Tài khoản bị khóa, không cho thử lại                      | Fail      |
| BVA-04 | Sai đúng 3 lần — kích hoạt lockout        | email đúng, pass sai × 3               | On-point (attempts=3)            | Tài khoản bị khóa 30 giây, hiển thị thông báo  | Vẫn bị khóa, không hiển thị thông báo khóa bao lâu        | Fail      |
| BVA-05 | Sai 4 lần — vượt biên                     | email đúng, pass sai × 4               | Just above boundary (attempts=4) | Vẫn bị khóa                                    | Tài khoản vẫn bị khóa                                     | Pass      |
| BVA-06 | Login đúng sau 29 giây (vẫn còn locked)   | email đúng, pass đúng, sau 29s         | Just below time boundary (29s)   | Vẫn bị khóa, không vào được                    | Vẫn bị khóa, không vào được                               | Pass      |
| BVA-07 | Login đúng sau đúng 30 giây               | email đúng, pass đúng, sau 30s         | On-point time boundary (30s)     | Đăng nhập thành công                           | Tài khoản vẫn bị khóa, đăng nhập thất bại                 | Fail      |
| BVA-08 | Login đúng sau 31 giây                    | email đúng, pass đúng, sau 31s         | Just above time boundary (31s)   | Đăng nhập thành công                           | Tài khoản vẫn bị khóa, đăng nhập thất bại                 | Fail      |
| BVA-09 | Email rỗng                                | email="", pass đúng                    | On-point empty email             | Báo bắt buộc nhập email                        | Hiển thị thông báo trường dữ liệu bắt buộc nhập           | Pass      |
| BVA-10 | Email format tối thiểu hợp lệ             | email=a@b.c, pass đúng                 | On-point minimum valid email     | Cho submit (server validate tồn tại)           | Cho phép submit, thông báo đăng nhập thất bại             | Pass      |
| BVA-11 | Email thiếu @                             | email=usergmail.com                    | Invalid email boundary           | HTML5 báo sai format                           | Chỉ hiển thị thông báo "Đăng nhập thất bại"               | Fail      |
| BVA-12 | Email thiếu local part                    | email=@gmail.com                       | Invalid email boundary           | HTML5 báo sai format                           | Chỉ hiển thị thông báo "Đăng nhập thất bại"               | Fail      |
| BVA-13 | Email thiếu domain                        | email=user@                            | Invalid email boundary           | HTML5 báo sai format                           | Chỉ hiển thị thông báo "Đăng nhập thất bại"               | Fail      |
| BVA-14 | Password rỗng                             | email đúng, pass=""                    | On-point empty password          | Báo bắt buộc nhập password                     | Hiển thị thông báo trường dữ liệu bắt buộc nhập           | Pass      |
| BVA-15 | Password 1 ký tự                          | email đúng, pass=a                     | Just above empty                 | Cho submit, server báo sai thông tin đăng nhập | Cho phép submit, thông báo đăng nhập thất bại             | Pass      |
| BVA-16 | Login đúng sau lockout, bộ đếm reset về 0 | email đúng, pass đúng, sau 30s         | Post-lockout reset               | Thành công, bộ đếm về 0                        | Login thành công sau lockout, sai tiếp không bị khóa ngay | Pass      |

#### 4.1.7 Bug Found

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

#### 4.1.8 Gap Analysis

| Gap            | Mô tả                              | Lý do AI bỏ sót                          |
| -------------- | ---------------------------------- | ---------------------------------------- |
| BUG-01, BUG-03 | Lỗi UI/visual và validation format | AI không chạy app thực tế để quan sát UI |
| BUG-02         | Silent lockout                     | AI tin spec và không kiểm tra UX thực tế |
| BUG-04         | Off-by-one attempts                | Cần BVA đúng điểm biên 2/3               |
| BUG-05         | Lockout timing                     | Cần đo thời gian thực tế trên hệ thống   |
| BUG-06         | Trim khoảng trắng                  | AI không biết behavior backend           |
| BUG-07         | Case-insensitive email             | Cần đối chiếu hành vi thực tế            |
| BUG-08         | Role escalation                    | Cần test bảo mật qua API trực tiếp       |

#### 4.1.9 Test Summary

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

| Item         | Detail                                   |
| ------------ | ---------------------------------------- |
| Feature ID   | FR-08                                    |
| Feature Name | Thanh toán (Checkout)                    |
| URL          | http://localhost:5173/checkout           |
| Technique    | Domain Testing + Boundary Value Analysis |

#### 4.2.2 Mô tả

Người dùng đã đăng nhập có thể tiến hành thanh toán từ giỏ hàng. Tổng tiền được tính tự động từ giỏ hàng, backend phải tự tính lại và không chấp nhận giá trị `total_amount` do client gửi lên. Sau thanh toán thành công, giỏ hàng phải được xóa.

#### 4.2.3 Domain Testing — Step-by-step

##### Bước 1: Xác định biến input

| Biến           | Kiểu    | Mô tả                             |
| -------------- | ------- | --------------------------------- |
| `auth_status`  | Boolean | Người dùng đã đăng nhập hay chưa  |
| `cart_items`   | List    | Danh sách sản phẩm trong giỏ hàng |
| `total_amount` | Float   | Tổng tiền thanh toán              |
| `user_profile` | Object  | Thông tin cá nhân (địa chỉ, SĐT)  |

##### Bước 2: Phân vùng miền

###### `auth_status`

| Miền | Loại    | Điều kiện      | Kết quả mong đợi            |
| ---- | ------- | -------------- | --------------------------- |
| A1   | Valid   | Đã đăng nhập   | Cho phép vào trang checkout |
| A2   | Invalid | Chưa đăng nhập | Redirect về trang login     |

###### `cart_items`

| Miền | Loại    | Điều kiện                 | Kết quả mong đợi                      |
| ---- | ------- | ------------------------- | ------------------------------------- |
| C1   | Valid   | Giỏ có ít nhất 1 sản phẩm | Hiển thị danh sách, cho phép checkout |
| C2   | Invalid | Giỏ rỗng                  | Không cho phép checkout               |
| C3   | Edge    | Giỏ có 1 sản phẩm         | Hiển thị đúng, cho phép checkout      |
| C4   | Edge    | Giỏ có nhiều sản phẩm     | Hiển thị đầy đủ, tính tổng đúng       |

###### `total_amount`

| Miền | Loại    | Điều kiện                     | Kết quả mong đợi         |
| ---- | ------- | ----------------------------- | ------------------------ |
| T1   | Valid   | Tổng tiền do backend tính     | Đúng với giỏ hàng        |
| T2   | Invalid | Client tự sửa `total_amount`  | Backend bỏ qua, tính lại |
| T3   | Invalid | Client gửi `total_amount = 0` | Backend bỏ qua, tính lại |
| T4   | Invalid | Client gửi `total_amount` âm  | Backend bỏ qua, tính lại |

###### `user_profile`

| Miền | Loại    | Điều kiện                | Kết quả mong đợi          |
| ---- | ------- | ------------------------ | ------------------------- |
| U1   | Valid   | Đầy đủ địa chỉ, SĐT      | Cho phép checkout         |
| U2   | Invalid | Thiếu địa chỉ            | Báo lỗi hay chặn checkout |
| U3   | Invalid | Thiếu SĐT                | Báo lỗi hay chặn checkout |
| U4   | Invalid | Thiếu cả địa chỉ lẫn SĐT | Báo lỗi hay chặn checkout |

##### Bước 3: Chọn test case đại diện

#### 4.2.4 Domain Testing — Test Case Table

| TC ID | Mô tả                                    | auth             | cart              | total_amount    | profile          | Kết quả mong đợi                                      | Kết quả thực tế                                                           | Pass/Fail |
| ----- | ---------------------------------------- | ---------------- | ----------------- | --------------- | ---------------- | ----------------------------------------------------- | ------------------------------------------------------------------------- | --------- |
| TC-01 | Checkout thành công đầy đủ thông tin     | A1 logged in     | C1 có hàng        | T1 backend tính | U1 đầy đủ        | Thanh toán thành công, giỏ hàng xóa, lịch sử cập nhật | Giỏ hàng không được xóa sau thanh toán thành công                         | Fail      |
| TC-02 | Chưa đăng nhập vào trang checkout        | A2 not logged in | C1 có hàng        | -               | -                | Redirect về `/login`                                  | Hiển thị thông báo "Cần đăng nhập để thanh toán", redirect về trang login | Pass      |
| TC-03 | Giỏ hàng rỗng                            | A1 logged in     | C2 rỗng           | -               | U1 đầy đủ        | Báo giỏ hàng trống                                    | Thông báo "Giỏ hàng trống", không cho checkout                            | Pass      |
| TC-04 | Giỏ hàng 1 sản phẩm                      | A1 logged in     | C3 1 sản phẩm     | T1 backend tính | U1 đầy đủ        | Hiển thị đúng 1 sản phẩm                              | Hiển thị đúng số tiền thanh toán                                          | Pass      |
| TC-05 | Giỏ hàng nhiều sản phẩm                  | A1 logged in     | C4 nhiều sản phẩm | T1 backend tính | U1 đầy đủ        | Hiển thị đầy đủ, tổng tiền đúng                       | Hiển thị đúng số tiền thanh toán                                          | Pass      |
| TC-06 | Client tự sửa total_amount trên UI       | A1 logged in     | C1 có hàng        | T2 client sửa   | U1 đầy đủ        | Backend tính lại đúng                                 | Không tính lại giá trị đúng, chấp nhận sửa đổi từ client                  | Fail      |
| TC-07 | Client gửi total_amount = 0              | A1 logged in     | C1 có hàng        | T3 = 0          | U1 đầy đủ        | Backend tính lại đúng, không chấp nhận 0              | Chấp nhận total_amount = 0                                                | Fail      |
| TC-08 | Client gửi total_amount âm               | A1 logged in     | C1 có hàng        | T4 âm           | U1 đầy đủ        | Backend tính lại đúng, không chấp nhận giá trị âm     | Chấp nhận giá trị total_amount âm                                         | Fail      |
| TC-09 | Thiếu địa chỉ vẫn checkout               | A1 logged in     | C1 có hàng        | T1 backend tính | U2 thiếu địa chỉ | Báo lỗi thiếu địa chỉ                                 | Vẫn checkout thành công khi chưa có địa chỉ nhận hàng                     | Fail      |
| TC-10 | Thiếu SĐT vẫn checkout                   | A1 logged in     | C1 có hàng        | T1 backend tính | U3 thiếu SĐT     | Báo lỗi thiếu SĐT                                     | Vẫn checkout thành công khi chưa có số điện thoại                         | Fail      |
| TC-11 | Thiếu cả địa chỉ lẫn SĐT                 | A1 logged in     | C1 có hàng        | T1 backend tính | U4 thiếu hết     | Báo lỗi thiếu thông tin                               | Vẫn checkout thành công                                                   | Fail      |
| TC-12 | Sau checkout thành công giỏ hàng xóa     | A1 logged in     | C1 có hàng        | T1 backend tính | U1 đầy đủ        | Giỏ hàng rỗng sau khi thanh toán                      | Không xóa giỏ hàng sau khi thanh toán thành công                          | Fail      |
| TC-13 | Sau checkout thành công lịch sử cập nhật | A1 logged in     | C1 có hàng        | T1 backend tính | U1 đầy đủ        | Đơn hàng xuất hiện trong lịch sử                      | Lịch sử được cập nhật chính xác                                           | Pass      |
| TC-14 | Tổng tiền hiển thị đúng với giỏ hàng     | A1 logged in     | C4 nhiều sản phẩm | T1 backend tính | U1 đầy đủ        | Tổng tiền = sum(giá × số lượng)                       | Tổng tiền được tính chính xác                                             | Pass      |

#### 4.2.5 BVA — Boundary Analysis

##### Ràng buộc

| Biến                  | Ràng buộc                                         | Nguồn           |
| --------------------- | ------------------------------------------------- | --------------- |
| cart_items            | Số lượng sản phẩm trong giỏ ≥ 1 mới checkout được | Spec + observed |
| total_amount          | Phải > 0, do backend tính                         | Spec            |
| quantity mỗi sản phẩm | Số lượng từng sản phẩm ≥ 1                        | Observed        |

##### cart_items — Số lượng sản phẩm trong giỏ

| Điểm biên            | Số sản phẩm | Tên              | Kết quả mong đợi                  |
| -------------------- | ----------- | ---------------- | --------------------------------- |
| On-point (biên dưới) | 0 sản phẩm  | Giỏ rỗng         | Không cho checkout                |
| Just above boundary  | 1 sản phẩm  | Tối thiểu hợp lệ | Cho phép checkout                 |
| In-range             | 2+ sản phẩm | Bình thường      | Cho phép checkout, tính đúng tổng |

##### total_amount — Tổng tiền

| Điểm biên                     | Giá trị             | Kết quả mong đợi                   |
| ----------------------------- | ------------------- | ---------------------------------- |
| Below boundary                | `total_amount < 0`  | Backend từ chối / tính lại         |
| On-point                      | `total_amount = 0`  | Backend từ chối / tính lại         |
| Just above boundary           | `total_amount = 1đ` | Backend chấp nhận nếu đúng với giỏ |
| Client sửa thành giá trị khác | Bất kỳ              | Backend bỏ qua, tự tính lại        |

##### quantity — Số lượng từng sản phẩm

| Điểm biên            | Giá trị           | Kết quả mong đợi         |
| -------------------- | ----------------- | ------------------------ |
| On-point (biên dưới) | 0 sản phẩm        | Không hợp lệ             |
| Just above boundary  | 1 sản phẩm        | Hợp lệ, tính đúng giá    |
| In-range             | 2, 5, 10 sản phẩm | Tính đúng giá × số lượng |

#### 4.2.6 BVA — Test Case Table

| TC ID  | Mô tả                                  | Input               | Biên liên quan           | Kết quả mong đợi                       | Kết quả thực tế                                                             | Pass/Fail |
| ------ | -------------------------------------- | ------------------- | ------------------------ | -------------------------------------- | --------------------------------------------------------------------------- | --------- |
| BVA-01 | Giỏ hàng rỗng (0 sản phẩm)             | cart = 0 items      | On-point cart=0          | Không cho checkout, báo giỏ trống      | Hiển thị thông báo "Giỏ hàng trống"                                         | Pass      |
| BVA-02 | Giỏ hàng 1 sản phẩm — tối thiểu hợp lệ | cart = 1 item       | Just above (cart=1)      | Cho checkout, hiển thị đúng 1 sản phẩm | Hiển thị đúng 1 sản phẩm, cho phép thanh toán                               | Pass      |
| BVA-03 | Giỏ hàng 2 sản phẩm                    | cart = 2 items      | In-range (cart=2)        | Cho checkout, tổng tiền đúng           | Hiển thị đúng 2 sản phẩm, cho phép thanh toán                               | Pass      |
| BVA-04 | total_amount = 0 do client gửi         | client gửi total=0  | On-point total=0         | Backend từ chối hoặc tính lại đúng     | Backend chấp nhận sửa đổi từ client                                         | Fail      |
| BVA-05 | total_amount âm do client gửi          | client gửi total=-1 | Below boundary total<0   | Backend từ chối hoặc tính lại đúng     | Backend chấp nhận, hiển thị số âm                                           | Fail      |
| BVA-06 | total_amount = 1đ hợp lệ               | sản phẩm giá 1đ     | Just above total=1       | Backend chấp nhận nếu khớp giỏ hàng    | Backend chấp nhận mọi mức giá sửa đổi từ client                             | Fail      |
| BVA-07 | Client sửa total_amount nhỏ hơn thực   | DevTools: sửa total | Client-side manipulation | Backend tính lại đúng                  | Backend chấp nhận mọi mức giá sửa đổi từ client, kể cả nhỏ hơn giá trị thực | Fail      |
| BVA-08 | Client sửa total_amount lớn hơn thực   | DevTools: sửa total | Client-side manipulation | Backend tính lại đúng                  | Backend chấp nhận mọi mức giá sửa đổi từ client, kể cả lớn hơn giá trị thực | Fail      |
| BVA-09 | Số lượng sản phẩm = 0 trong giỏ        | quantity = 0        | On-point quantity=0      | Không cho thêm vào giỏ hoặc tự xóa     | Sản phẩm tự động được xóa khỏi giỏ hàng                                     | Pass      |
| BVA-10 | Số lượng sản phẩm = 1                  | quantity = 1        | Just above quantity=1    | Tính đúng giá × 1                      | Backend tính đúng giá                                                       | Pass      |
| BVA-11 | Số lượng sản phẩm lớn                  | quantity = 999      | Far above boundary       | Tính đúng tổng tiền, không overflow    | Chưa thực thi                                                               | NA        |

#### 4.2.7 Bug Found

| Bug ID  | Severity | Summary                                                 | Nguồn phát hiện |
| ------- | -------- | ------------------------------------------------------- | --------------- |
| FR08-01 | Critical | Tổng tiền checkout cho phép chỉnh sửa trực tiếp trên UI | Domain, BVA     |
| FR08-02 | Critical | Backend chấp nhận `total_amount` do client sửa          | Domain, BVA     |
| FR08-03 | Major    | Checkout thành công nhưng giỏ hàng không bị xóa         | Domain          |
| FR08-04 | Major    | Vẫn cho checkout khi thiếu địa chỉ/SĐT nhận hàng        | Domain          |

#### 4.2.8 Gap Analysis

| Gap    | Mô tả                           | Lý do AI bỏ sót                                 |
| ------ | ------------------------------- | ----------------------------------------------- |
| BUG-01 | UI cho sửa total_amount         | AI không render UI thực tế                      |
| BUG-02 | Giỏ hàng không xóa sau checkout | AI giả định backend cập nhật đúng               |
| BUG-03 | Checkout khi thiếu profile      | AI không nối ràng buộc giữa profile và checkout |
| BUG-04 | Client sửa total_amount         | Cần thao tác DevTools/intercept request         |
| BVA-11 | quantity=999 chưa chạy          | AI không tự sinh stress test                    |

#### 4.2.9 Test Summary

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

| Item         | Detail                                   |
| ------------ | ---------------------------------------- |
| Feature ID   | FR-14                                    |
| Feature Name | Quản lý Danh mục (Category CRUD)         |
| URL          | http://localhost:5173/admin/categories   |
| Technique    | Domain Testing + Boundary Value Analysis |

#### 4.3.2 Mô tả

Admin có thể thêm / xem / xóa danh mục. Tên danh mục là bắt buộc và không được để trống.

#### 4.3.3 Domain Testing — Step-by-step

##### Bước 1: Xác định biến input

| Biến            | Kiểu    | Mô tả                                                |
| --------------- | ------- | ---------------------------------------------------- |
| `auth_role`     | Enum    | Quyền truy cập: admin / user thường / chưa đăng nhập |
| `category_name` | String  | Tên danh mục khi thêm mới                            |
| `category_id`   | Integer | ID danh mục khi xóa                                  |

##### Bước 2: Phân vùng miền

###### `auth_role`

| Miền | Loại    | Điều kiện                          | Kết quả mong đợi                     |
| ---- | ------- | ---------------------------------- | ------------------------------------ |
| R1   | Valid   | Đã đăng nhập với quyền Admin       | Truy cập được trang quản lý danh mục |
| R2   | Invalid | Đã đăng nhập với quyền User thường | Bị từ chối truy cập (403)            |
| R3   | Invalid | Chưa đăng nhập                     | Redirect về trang login              |

###### `category_name` khi thêm mới

| Miền | Loại    | Điều kiện                    | Ví dụ                       |
| ---- | ------- | ---------------------------- | --------------------------- |
| N1   | Valid   | Tên hợp lệ, không rỗng       | "Điện thoại"                |
| N2   | Valid   | Tên có khoảng trắng ở giữa   | "Đồ gia dụng"               |
| N3   | Invalid | Rỗng hoàn toàn               | ""                          |
| N4   | Invalid | Chỉ có khoảng trắng          | " "                         |
| N5   | Edge    | Tên rất dài                  | 255+ ký tự                  |
| N6   | Edge    | Tên trùng với danh mục đã có | "Điện thoại"                |
| N7   | Edge    | Tên có ký tự đặc biệt        | "<script>alert(1)</script>" |
| N8   | Edge    | Tên chỉ có số                | "12345"                     |

###### `category_id` khi xóa

| Miền | Loại    | Điều kiện                 | Kết quả mong đợi                   |
| ---- | ------- | ------------------------- | ---------------------------------- |
| I1   | Valid   | ID tồn tại trong DB       | Xóa thành công                     |
| I2   | Invalid | ID không tồn tại          | Báo lỗi không tìm thấy             |
| I3   | Edge    | Danh mục đang có sản phẩm | Cần xác định: xóa được hay báo lỗi |

##### Bước 3: Chọn test case đại diện

#### 4.3.4 Domain Testing — Test Case Table

| TC ID | Mô tả                                 | auth_role        | category_name                 | category_id    | Kết quả mong đợi                  | Kết quả thực tế                                   | Pass/Fail |
| ----- | ------------------------------------- | ---------------- | ----------------------------- | -------------- | --------------------------------- | ------------------------------------------------- | --------- |
| TC-01 | Admin xem danh sách danh mục          | R1 admin         | -                             | -              | Hiển thị đầy đủ danh sách         | Hiển thị đầy đủ danh sách                         | Pass      |
| TC-02 | User thường truy cập trang admin      | R2 user          | -                             | -              | Bị từ chối, báo 403 hoặc redirect | Bị từ chối quyền truy cập                         | Pass      |
| TC-03 | Chưa đăng nhập truy cập trang admin   | R3 not logged in | -                             | -              | Redirect về `/login`              | Redirect về `/login`                              | Pass      |
| TC-04 | Thêm danh mục hợp lệ                  | R1 admin         | N1 "Điện thoại"               | -              | Thêm thành công                   | Thêm thành công                                   | Pass      |
| TC-05 | Thêm danh mục có khoảng trắng giữa    | R1 admin         | N2 "Đồ gia dụng"              | -              | Thêm thành công                   | Thêm thành công                                   | Pass      |
| TC-06 | Thêm danh mục tên rỗng                | R1 admin         | N3 ""                         | -              | Báo lỗi bắt buộc nhập             | Thêm thành công với chuỗi rỗng                    | Fail      |
| TC-07 | Thêm danh mục chỉ có khoảng trắng     | R1 admin         | N4 " "                        | -              | Báo lỗi bắt buộc nhập             | Thêm thành công với chuỗi rỗng                    | Fail      |
| TC-08 | Thêm danh mục tên rất dài             | R1 admin         | N5 255+ ký tự                 | -              | Báo lỗi hoặc cắt bớt              | Thêm thành công                                   | Fail      |
| TC-09 | Thêm danh mục trùng tên               | R1 admin         | N6 tên trùng                  | -              | Báo lỗi tên đã tồn tại            | Vẫn thêm thành công, hiển thị trùng lặp           | Fail      |
| TC-10 | Thêm danh mục tên có script injection | R1 admin         | N7 `<script>`                 | -              | Sanitize hoặc báo lỗi             | Thêm thành công với tên hiển thị dạng text        | Pass      |
| TC-11 | Thêm danh mục tên chỉ có số           | R1 admin         | N8 "12345"                    | -              | Thêm thành công                   | Thêm thành công                                   | Pass      |
| TC-12 | Xóa danh mục tồn tại                  | R1 admin         | -                             | I1 valid ID    | Xóa thành công                    | Xóa thành công                                    | Pass      |
| TC-13 | Xóa danh mục đang có sản phẩm         | R1 admin         | -                             | I3 có sản phẩm | Cảnh báo / chặn xóa               | Vẫn xóa được, không có thông báo                  | Fail      |
| TC-14 | Xem danh sách sau khi thêm            | R1 admin         | N1 mới thêm                   | -              | Danh mục mới xuất hiện            | Danh mục mới xuất hiện                            | Pass      |
| TC-15 | Xem danh sách sau khi xóa             | R1 admin         | -                             | I1 vừa xóa     | Danh mục đã xóa biến mất          | Danh mục đã xóa biến mất                          | Pass      |
| TC-16 | Dùng token user thường gọi API thêm   | R2 user token    | N1 valid name                 | -              | Bị từ chối 403                    | Dùng user token vẫn thêm được category mới        | Fail      |
| TC-17 | Tên danh mục có SQL injection         | R1 admin         | `'; DROP TABLE categories;--` | -              | Sanitize, không execute SQL       | Không execute SQL, thêm tên dạng text bình thường | Pass      |

#### 4.3.5 BVA — Boundary Analysis

##### Ràng buộc

| Biến          | Ràng buộc                        | Nguồn      |
| ------------- | -------------------------------- | ---------- |
| category_name | Không được rỗng (length > 0)     | Spec       |
| category_name | Độ dài tối đa chưa rõ trong spec | Cần verify |

##### category_name — Độ dài

| Điểm biên           | Độ dài   | Tên              | Kết quả mong đợi      |
| ------------------- | -------- | ---------------- | --------------------- |
| On-point            | 0 ký tự  | Rỗng hoàn toàn   | Báo lỗi bắt buộc nhập |
| Just above boundary | 1 ký tự  | Tối thiểu hợp lệ | Thêm thành công       |
| In-range            | 2 ký tự  | Bình thường      | Thêm thành công       |
| In-range            | 50 ký tự | Thông thường     | Thêm thành công       |

##### Biên trên của category_name

| Điểm biên      | Độ dài     | Kết quả mong đợi             |
| -------------- | ---------- | ---------------------------- |
| In-range       | 100 ký tự  | Thêm thành công              |
| Test boundary  | 255 ký tự  | Thêm thành công hoặc báo lỗi |
| Above boundary | 256 ký tự  | Báo lỗi nếu VARCHAR(255)     |
| Far above      | 1000 ký tự | Báo lỗi                      |

##### Khoảng trắng (whitespace)

| Điểm biên           | Giá trị         | Kết quả mong đợi     |
| ------------------- | --------------- | -------------------- |
| On-point whitespace | " "             | Báo lỗi sau khi trim |
| Just above          | " a"            | Thêm thành công      |
| Valid               | "Máy tính bảng" | Thêm thành công      |

#### 4.3.6 BVA — Test Case Table

| TC ID  | Mô tả                            | Input      | Biên liên quan            | Kết quả mong đợi                  | Kết quả thực tế                                                     | Pass/Fail |
| ------ | -------------------------------- | ---------- | ------------------------- | --------------------------------- | ------------------------------------------------------------------- | --------- |
| BVA-01 | Tên rỗng hoàn toàn               | ""         | On-point length=0         | Báo lỗi bắt buộc nhập tên         | Không thông báo lỗi, thêm thành công danh mục mới với nội dung rỗng | Fail      |
| BVA-02 | Tên 1 ký tự                      | "a"        | Just above length=1       | Thêm thành công                   | Thêm thành công                                                     | Pass      |
| BVA-03 | Tên 2 ký tự                      | "ab"       | In-range length=2         | Thêm thành công                   | Thêm thành công                                                     | Pass      |
| BVA-04 | Tên 255 ký tự                    | 255 ký tự  | Upper boundary            | Thêm thành công hoặc báo lỗi      | Thêm thành công                                                     | Pass      |
| BVA-05 | Tên 256 ký tự                    | 256 ký tự  | Just above upper boundary | Báo lỗi nếu giới hạn VARCHAR(255) | Thêm thành công                                                     | Pass      |
| BVA-06 | Tên 1000 ký tự                   | 1000 ký tự | Far above upper boundary  | Báo lỗi                           | Vẫn thêm thành công                                                 | Fail      |
| BVA-07 | Tên chỉ có 1 khoảng trắng        | " "        | On-point whitespace       | Báo lỗi sau khi trim              | Có trim text nhưng không báo lỗi chuỗi rỗng                         | Fail      |
| BVA-08 | Tên chỉ có nhiều khoảng trắng    | " "        | On-point whitespace       | Báo lỗi sau khi trim              | Có trim text nhưng không báo lỗi chuỗi rỗng                         | Fail      |
| BVA-09 | Tên có khoảng trắng đầu + ký tự  | " a"       | Just above whitespace     | Thêm thành công, trim space đầu   | Có trim text và thêm thành công                                     | Pass      |
| BVA-10 | Tên có khoảng trắng cuối + ký tự | "a "       | Just above whitespace     | Thêm thành công, trim space cuối  | Có trim text và thêm thành công                                     | Pass      |

#### 4.3.7 Bug Found

| Bug ID  | Severity | Summary                                              | Nguồn phát hiện |
| ------- | -------- | ---------------------------------------------------- | --------------- |
| FR14-01 | Major    | Thêm được danh mục với tên rỗng                      | Domain, BVA     |
| FR14-02 | Minor    | Tên chỉ chứa khoảng trắng vẫn thêm được              | Domain, BVA     |
| FR14-03 | Minor    | Không giới hạn độ dài tên danh mục                   | BVA             |
| FR14-04 | Major    | Cho phép thêm danh mục trùng tên                     | Domain          |
| FR14-05 | Major    | Xóa danh mục đang có sản phẩm liên kết               | Domain          |
| FR14-06 | Critical | User thường dùng token vẫn gọi được API tạo danh mục | Domain          |

#### 4.3.8 Gap Analysis

| Gap          | Mô tả                     | Lý do AI bỏ sót                       |
| ------------ | ------------------------- | ------------------------------------- |
| BUG-01       | Thêm được danh mục rỗng   | AI giả định validation đúng           |
| BUG-02       | Tên chỉ có khoảng trắng   | AI không tự nghĩ tới trim() edge case |
| BUG-03       | Không giới hạn độ dài tên | AI không biết schema DB               |
| BUG-04       | Trùng tên vẫn thêm được   | AI không biết unique constraint       |
| BUG-05       | Xóa danh mục có sản phẩm  | AI không biết quan hệ dữ liệu thực tế |
| BUG-06       | Role bypass qua API       | Cần test security trực tiếp           |
| TC-10, TC-17 | XSS/SQL injection pass    | Đây không phải gap nhưng cần lưu ý    |

#### 4.3.9 Test Summary

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

| Item         | Detail                                   |
| ------------ | ---------------------------------------- |
| Feature ID   | FR-20                                    |
| Feature Name | Đăng nhập trên Mobile App                |
| Platform     | React Native (Expo Go)                   |
| Technique    | Domain Testing + Boundary Value Analysis |

#### 4.4.2 Mô tả

Người dùng nhập email và mật khẩu để đăng nhập trên mobile app. Sau đăng nhập thành công nhận JWT Token, redirect về trang chủ. Sai từ 3 lần trở lên bị lockout 30 giây.

#### 4.4.3 Domain Testing — Step-by-step

##### Bước 1: Xác định biến input

| Biến             | Kiểu    | Mô tả                          |
| ---------------- | ------- | ------------------------------ |
| `email`          | String  | Địa chỉ email đăng nhập        |
| `password`       | String  | Mật khẩu đăng nhập             |
| `login_attempts` | Integer | Số lần đăng nhập sai liên tiếp |

##### Bước 2: Phân vùng miền

###### `email`

| Miền | Loại    | Điều kiện                           | Ví dụ                |
| ---- | ------- | ----------------------------------- | -------------------- |
| E1   | Valid   | Email đúng format, tồn tại trong DB | `user@gmail.com`     |
| E2   | Invalid | Sai format, không có `@`            | `usergmail.com`      |
| E3   | Invalid | Sai format, thiếu domain            | `user@`              |
| E4   | Invalid | Đúng format nhưng không tồn tại     | `notexist@gmail.com` |
| E5   | Edge    | Rỗng                                | `""`                 |
| E6   | Edge    | Chỉ có khoảng trắng                 | `"   "`              |
| E7   | Edge    | Email chữ HOA                       | `USER@GMAIL.COM`     |

###### `password`

| Miền | Loại    | Điều kiện                           | Ví dụ             |
| ---- | ------- | ----------------------------------- | ----------------- |
| P1   | Valid   | Đúng password của tài khoản         | `correctPass123!` |
| P2   | Invalid | Sai password                        | `wrongPass`       |
| P3   | Edge    | Rỗng                                | `""`              |
| P4   | Edge    | Password đúng nhưng khác hoa/thường | `CORRECTPASS123!` |

###### `login_attempts`

| Miền | Loại         | Giá trị     | Hành vi mong đợi          |
| ---- | ------------ | ----------- | ------------------------- |
| A1   | Valid        | 0 lần sai   | Cho đăng nhập bình thường |
| A2   | Valid        | 1-2 lần sai | Báo lỗi, cho thử lại      |
| A3   | Boundary     | 3 lần sai   | Lockout 30 giây           |
| A4   | Invalid      | 4+ lần sai  | Vẫn bị khóa               |
| A5   | Post-lockout | Sau 30 giây | Cho đăng nhập lại         |

##### Bước 3: Chọn test case đại diện

#### 4.4.4 Domain Testing — Test Case Table

| TC ID | Mô tả                                | Email                 | Password       | Attempts     | Kết quả mong đợi                               | Kết quả thực tế                                                     | Pass/Fail |
| ----- | ------------------------------------ | --------------------- | -------------- | ------------ | ---------------------------------------------- | ------------------------------------------------------------------- | --------- |
| TC-01 | Đăng nhập thành công                 | E1 valid              | P1 correct     | A1 (0)       | Redirect về home                               | Đăng nhập thành công, điều hướng chính xác                          | Pass      |
| TC-02 | Email rỗng                           | E5 empty              | P1 correct     | A1 (0)       | Báo lỗi bắt buộc nhập email                    | Không báo lỗi bắt buộc, chỉ thông báo "Đăng nhập thất bại"          | Fail      |
| TC-03 | Password rỗng                        | E1 valid              | P3 empty       | A1 (0)       | Báo lỗi bắt buộc nhập password                 | Không báo lỗi buộc nhập, cho phép submit và tính login_attempts = 1 | Fail      |
| TC-04 | Cả 2 rỗng                            | E5 empty              | P3 empty       | A1 (0)       | Báo lỗi bắt buộc nhập                          | Không báo lỗi bắt buộc, chỉ thông báo "Đăng nhập thất bại"          | Fail      |
| TC-05 | Email sai format (không có @)        | E2 usergmail.com      | P1 correct     | A1 (0)       | Báo sai format email                           | Không báo lỗi format, chỉ thông báo "Đăng nhập thất bại"            | Fail      |
| TC-06 | Email không tồn tại                  | E4 notexist@gmail.com | P2 wrong       | A1 (0)       | Báo đăng nhập thất bại                         | Thông báo "Đăng nhập thất bại"                                      | Pass      |
| TC-07 | Sai password lần 1                   | E1 valid              | P2 wrong       | A2 (1)       | Báo "Đăng nhập thất bại"                       | Thông báo "Đăng nhập thất bại", cho phép thử lại                    | Pass      |
| TC-08 | Sai password lần 2                   | E1 valid              | P2 wrong       | A2 (2)       | Báo lỗi, cho thử lại                           | Báo lỗi, khóa tài khoản                                             | Fail      |
| TC-09 | Sai password lần 3 — trigger lockout | E1 valid              | P2 wrong       | A3 (3)       | Thông báo tài khoản bị khóa 30 giây            | Khóa tài khoản sau 3 lần sai                                        | Pass      |
| TC-10 | Đăng nhập đúng khi đang lockout      | E1 valid              | P1 correct     | A3 (locked)  | Vẫn không vào được, hiển thị thông báo bị khóa | Không đăng nhập được nhưng không có thông báo bị khóa rõ ràng       | Fail      |
| TC-11 | Đăng nhập đúng sau lockout (>30s)    | E1 valid              | P1 correct     | A5 (sau 30s) | Đăng nhập thành công                           | Vẫn bị khóa, tiếp tục chờ đợi                                       | Fail      |
| TC-12 | Email chữ HOA                        | E7 USER@GMAIL.COM     | P1 correct     | A1 (0)       | Đăng nhập thành công                           | Đăng nhập thất bại                                                  | Fail      |
| TC-13 | Password đúng nhưng khác hoa/thường  | E1 valid              | P4 CORRECTPASS | A1 (0)       | Đăng nhập thất bại                             | Đăng nhập thất bại                                                  | Pass      |
| TC-14 | Bàn phím khi nhập email              | E1 valid              | -              | -            | Bàn phím loại email                            | Bàn phím loại thường                                                | Fail      |
| TC-15 | Password được ẩn bằng ký tự đặc biệt | E1 valid              | P1 correct     | A1 (0)       | Ký tự bị ẩn khi nhập password                  | Password bị ẩn bằng kí tự đặc biệt                                  | Pass      |

#### 4.4.5 BVA — Boundary Analysis

##### Ràng buộc

| Biến             | Ràng buộc                             | Nguồn       |
| ---------------- | ------------------------------------- | ----------- |
| email            | Không được rỗng                       | Observed    |
| password         | Không được rỗng, secureTextEntry=true | Source code |
| login_attempts   | Sai ≥ 3 lần → lockout 30 giây         | README spec |
| lockout_duration | 30 giây                               | README spec |

##### login_attempts — Ngưỡng lockout

| Điểm biên           | Giá trị   | Tên                 | Kết quả mong đợi          |
| ------------------- | --------- | ------------------- | ------------------------- |
| In-range            | 1 lần sai | Dưới ngưỡng         | Báo lỗi, cho thử lại      |
| Just below boundary | 2 lần sai | Dưới biên           | Vẫn cho thử lại           |
| On-point            | 3 lần sai | Đúng ngưỡng lockout | Kích hoạt lockout 30 giây |
| Just above boundary | 4 lần sai | Vượt biên           | Vẫn bị khóa               |

##### lockout_duration — Thời gian khóa 30 giây

| Điểm biên           | Giá trị | Kết quả mong đợi       |
| ------------------- | ------- | ---------------------- |
| Just below boundary | 29 giây | Vẫn bị khóa            |
| On-point            | 30 giây | Tài khoản được mở khóa |
| Just above boundary | 31 giây | Đăng nhập thành công   |

##### email — Rỗng và không rỗng

| Điểm biên  | Giá trị     | Kết quả mong đợi             |
| ---------- | ----------- | ---------------------------- |
| On-point   | ""          | Báo bắt buộc nhập email      |
| Just above | 1 ký tự "a" | Cho submit (server validate) |

##### password — Rỗng và không rỗng

| Điểm biên  | Giá trị     | Kết quả mong đợi             |
| ---------- | ----------- | ---------------------------- |
| On-point   | ""          | Báo bắt buộc nhập password   |
| Just above | 1 ký tự "a" | Cho submit (server validate) |

#### 4.4.6 BVA — Test Case Table

| TC ID  | Mô tả                               | Input                             | Biên liên quan          | Kết quả mong đợi                            | Kết quả thực tế                                                      | Pass/Fail |
| ------ | ----------------------------------- | --------------------------------- | ----------------------- | ------------------------------------------- | -------------------------------------------------------------------- | --------- |
| BVA-01 | Sai 1 lần                           | email đúng, pass sai × 1          | In-range (attempts=1)   | Báo lỗi, cho thử lại                        | Báo lỗi, cho thử lại                                                 | Pass      |
| BVA-02 | Sai 2 lần                           | email đúng, pass sai × 2          | Just below (attempts=2) | Vẫn cho thử lại                             | Báo lỗi, khóa tài khoản, không cho phép đăng nhập                    | Fail      |
| BVA-03 | Sai đúng 3 lần                      | email đúng, pass sai × 3          | On-point (attempts=3)   | Lockout 30 giây, có thông báo rõ ràng       | Tài khoản bị khóa nhưng không thông báo rõ ràng                      | Fail      |
| BVA-04 | Sai 4 lần                           | email đúng, pass sai × 4          | Just above (attempts=4) | Vẫn bị khóa                                 | -                                                                    | -         |
| BVA-05 | Đăng nhập đúng sau 29 giây          | email đúng, pass đúng, sau 29s    | Just below time (29s)   | Vẫn không vào được                          | Tài khoản vẫn bị khóa                                                | Pass      |
| BVA-06 | Đăng nhập đúng sau đúng 30 giây     | email đúng, pass đúng, sau 30s    | On-point time (30s)     | Đăng nhập thành công                        | Đăng nhập thất bại vì tài khoản vẫn bị khóa                          | Fail      |
| BVA-07 | Đăng nhập đúng sau 31 giây          | email đúng, pass đúng, sau 31s    | Just above time (31s)   | Đăng nhập thành công                        | Đăng nhập thất bại vì tài khoản vẫn bị khóa                          | Fail      |
| BVA-08 | Email rỗng                          | "" email                          | On-point email=0 ký tự  | Báo bắt buộc nhập email                     | Không thông báo trường dữ liệu bắt buộc                              | Fail      |
| BVA-09 | Email 1 ký tự                       | "a" email                         | Just above email=1      | Cho submit, server báo sai format           | Cho phép submit, không thông báo sai format                          | Fail      |
| BVA-10 | Password rỗng                       | email đúng, "" password           | On-point password=0     | Báo bắt buộc nhập password                  | Không thông báo bắt buộc, cho phép submit và tính login_attempts = 1 | Fail      |
| BVA-11 | Password 1 ký tự                    | "a" password                      | Just above password=1   | Cho submit, server báo "Đăng nhập thất bại" | Cho phép submit, server báo "Đăng nhập thất bại"                     | Pass      |
| BVA-12 | Reset attempts sau login thành công | Sai 2 lần → login đúng → sai tiếp | Post-success reset      | Bộ đếm về 0, không bị lockout sớm           | Bộ đếm được reset sau khi đăng nhập đúng                             | Pass      |

#### 4.4.7 Bug Found

| Bug ID  | Severity | Summary                                                | Nguồn phát hiện |
| ------- | -------- | ------------------------------------------------------ | --------------- |
| FR20-01 | Minor    | Label email hiển thị "Username" thay vì "Email"        | Domain          |
| FR20-02 | Major    | Email/password rỗng không được validate client-side    | Domain, BVA     |
| FR20-03 | Minor    | Email sai format không được validate client-side       | Domain, BVA     |
| FR20-04 | Major    | Lockout kích hoạt sau 2 lần sai thay vì 3 lần          | Domain, BVA     |
| FR20-05 | Major    | Silent lockout, không hiển thị thông báo bị khóa riêng | Domain, BVA     |
| FR20-06 | Major    | Thời gian lockout thực tế ~3 phút thay vì 30 giây      | Domain, BVA     |
| FR20-07 | Minor    | Email viết hoa không đăng nhập được                    | Domain          |

#### 4.4.8 Gap Analysis

| Gap                | Mô tả                                   | Lý do AI bỏ sót                         |
| ------------------ | --------------------------------------- | --------------------------------------- |
| BVA-05/06/07       | Test timing 29s/30s/31s                 | AI không đo thời gian thực              |
| BUG-04             | Mobile có cùng bug timing như web không | AI không chạy app                       |
| BUG-02             | Bàn phím sai loại                       | Đặc thù UX mobile, cần quan sát thực tế |
| BUG-01, 03, 05, 07 | UI/validation/case handling             | AI không trải nghiệm app thực tế        |

#### 4.4.9 Test Summary

| Metric            | Value |
| ----------------- | ----- |
| Total test cases  | 27    |
| Executed          | 27    |
| Passed            | 9     |
| Failed            | 18    |
| Not executed      | 0     |
| Unique bugs found | 7     |

## 5. Kết luận

Tổng cộng 4 feature đã được kiểm thử bằng Domain Testing và Boundary Value Analysis. Report này tổng hợp đầy đủ các bước phân vùng miền, biên kiểm thử, bảng testcase, bug found và gap analysis cho từng feature. Kết quả cuối cùng là 25 bug unique trên 114 test case được thiết kế, trong đó 113 test case đã thực thi và 1 test case chưa thực thi.
