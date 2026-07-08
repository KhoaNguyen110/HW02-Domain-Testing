# FR-02: Login and Account Lockout — Domain Testing Report

## 1. Feature Overview

| Item         | Detail                      |
| ------------ | --------------------------- |
| Feature ID   | FR-02                       |
| Feature Name | Login and Account Lockout   |
| URL          | http://localhost:5173/login |
| Technique    | Domain Testing              |

### Description

Người dùng nhập Email và Mật khẩu để đăng nhập. Sau mỗi lần đăng nhập sai, hệ thống tăng bộ đếm lên 1. Nếu sai từ 3 lần trở lên liên tiếp, tài khoản bị tạm khóa 30 giây. Đăng nhập thành công trả về JWT Token, redirect về trang chủ.

## 2. Domain Testing — Step-by-step

### Bước 1: Xác định biến input

FR-02 có các biến input sau:

| Biến           | Kiểu    | Mô tả                                              |
| -------------- | ------- | -------------------------------------------------- |
| email          | String  | Địa chỉ email đăng nhập                            |
| password       | String  | Mật khẩu đăng nhập                                 |
| login_attempts | Integer | Số lần đăng nhập sai liên tiếp (ảnh hưởng lockout) |

### Bước 2: Phân tích và phân vùng miền (Domain Partitioning)

#### Biến email

| Miền | Loại    | Điều kiện                                 | Ví dụ              |
| ---- | ------- | ----------------------------------------- | ------------------ |
| D1   | Valid   | Đúng format email HTML5, tồn tại trong DB | user@example.com   |
| D2   | Invalid | Sai format — không có @                   | userexample.com    |
| D3   | Invalid | Sai format — thiếu domain                 | user@              |
| D4   | Invalid | Sai format — chỉ có @                     | @gmail.com         |
| D5   | Invalid | Đúng format nhưng không tồn tại trong DB  | notexist@gmail.com |
| D6   | Edge    | Rỗng (empty)                              | ``                 |
| D7   | Edge    | Chỉ có khoảng trắng                       | `   `              |
| D8   | Edge    | Email chữ HOA                             | USER@GMAIL.COM     |

#### Biến password

| Miền | Loại    | Điều kiện                           | Ví dụ          |
| ---- | ------- | ----------------------------------- | -------------- |
| P1   | Valid   | Đúng password của tài khoản         | correctPass123 |
| P2   | Invalid | Sai password                        | wrongPass      |
| P3   | Edge    | Rỗng (empty)                        | ``             |
| P4   | Edge    | Có khoảng trắng đầu/cuối            | ` pass`        |
| P5   | Edge    | Password đúng nhưng khác hoa/thường | CORRECTPASS123 |

#### Biến login_attempts (số lần sai liên tiếp)

| Miền | Loại         | Giá trị                    | Hành vi mong đợi          |
| ---- | ------------ | -------------------------- | ------------------------- |
| A1   | Valid        | 0 (chưa sai lần nào)       | Không bị ảnh hưởng        |
| A2   | Valid        | 1 lần sai                  | Báo lỗi, cho thử lại      |
| A3   | Valid        | 2 lần sai                  | Báo lỗi, cho thử lại      |
| A4   | Boundary     | 3 lần sai                  | Kích hoạt lockout 30 giây |
| A5   | Invalid      | 4+ lần sai (trong lockout) | Vẫn bị khóa               |
| A6   | Post-lockout | Sau 30 giây                | Cho phép thử lại          |

### Bước 3: Chọn test case đại diện cho mỗi miền

> **Nguyên tắc:** Mỗi miền chọn ít nhất 1 test case đại diện (on-point). Kết hợp các miền để tạo test scenario thực tế.

## 3. Test Case Table

| TC ID | Mô tả                                       | Email (Miền)          | Password (Miền) | Attempts             | Kết quả mong đợi                               | Kết quả thực tế                                                                     | Pass/Fail |
| ----- | ------------------------------------------- | --------------------- | --------------- | -------------------- | ---------------------------------------------- | ----------------------------------------------------------------------------------- | --------- |
| TC-01 | Đăng nhập thành công                        | D1 valid email        | P1 correct pass | A1 (0)               | Redirect về /, nhận JWT Token                  | Đăng nhập thành công, token tồn tại                                                 | Pass      |
| TC-02 | Email rỗng                                  | D6 empty              | P1 correct pass | A1 (0)               | Báo bắt buộc nhập email                        | Hiển thị thông báo trường dữ liệu bắt buộc nhập                                     | Pass      |
| TC-03 | Password rỗng                               | D1 valid email        | P3 empty        | A1 (0)               | Báo bắt buộc nhập password                     | Hiển thị thông báo trường dữ liệu bắt buộc nhập                                     | Pass      |
| TC-04 | Cả 2 field rỗng                             | D6 empty              | P3 empty        | A1 (0)               | Báo bắt buộc nhập                              | Hiển thị thông báo trường dữ liệu bắt buộc nhập                                     | Pass      |
| TC-05 | Email sai format (không có @)               | D2 userexample.com    | P1 correct pass | A1 (0)               | HTML5 validation báo sai format                | Chỉ hiển thị thông báo "Đăng nhập thất bại"                                         | Fail      |
| TC-06 | Email sai format (thiếu domain)             | D3 user@              | P1 correct pass | A1 (0)               | HTML5 validation báo sai format                | Chỉ hiển thị thông báo "Đăng nhập thất bại"                                         | Fail      |
| TC-07 | Email chỉ có @                              | D4 @gmail.com         | P1 correct pass | A1 (0)               | HTML5 validation báo sai format                | Chỉ hiển thị thông báo "Đăng nhập thất bại"                                         | Fail      |
| TC-08 | Email không tồn tại                         | D5 notexist@gmail.com | P2 wrong pass   | A1 (0)               | Báo đăng nhập thất bại                         | Hiển thị thông báo "Đăng nhập thất bại"                                             | Pass      |
| TC-09 | lần 1 Sai password                          | D1 valid email        | P2 wrong pass   | A2 (1)               | Báo đăng nhập thất bại, cho thử lại            | Hiển thị thông báo "Đăng nhập thất bại", cho phép thử lại                           | Pass      |
| TC-10 | Sai password lần 2                          | D1 valid email        | P2 wrong pass   | A3 (2)               | Báo đăng nhập thất bại, cho thử lại            | Hiển thị thông báo "Đăng nhập thất bại", tài khoản bị khóa, không cho phép thử lại  | Fial      |
| TC-11 | Sai password lần 3 — trigger lockout        | D1 valid email        | P2 wrong pass   | A4 (3)               | Tài khoản bị khóa 30 giây, hiển thị thông báo  | Tài khoản bị khóa, nhưng không hiển thị thông báo đến người dùng                    | Fail      |
| TC-12 | Login đúng pass khi đang bị lockout         | D1 valid email        | P1 correct pass | A4 (đang locked)     | Vẫn không vào được, hiển thị thông báo bị khóa | Tài khoản vẫn bị khóa, không hiển thị thông báo, không submit được                  | Fail      |
| TC-13 | Login sai tiếp khi đang bị lockout          | D1 valid email        | P2 wrong pass   | A5 (4+)              | Vẫn bị khóa                                    | Vẫn bị khóa                                                                         | Pass      |
| TC-14 | Login đúng sau khi hết lockout (>30s)       | D1 valid email        | P1 correct pass | A6 (sau 30s)         | Đăng nhập thành công, redirect về /            | Tài khoản vẫn bị khóa, người dùng phải chờ 3 phút                                   | Fail      |
| TC-15 | Password có khoảng trắng                    | D1 valid email        | P4 ` pass`      | A1 (0)               | Xác định hệ thống có trim không                | Hệ thống không tự động trim text khi có khoảng trắng, hiển thị "Đăng nhập thất bại" | Fail      |
| TC-16 | Email chữ HOA                               | D8 USER@GMAIL.COM     | P1 correct pass | A1 (0)               | Đăng nhập thành công (case-insensitive)        | Đăng nhập thất bại                                                                  | Fail      |
| TC-17 | Password đúng nhưng khác hoa/thường         | D1 valid email        | P5 CORRECTPASS  | A1 (0)               | Đăng nhập thất bại (case-sensitive)            | Đăng nhập thất bại                                                                  | Pass      |
| TC-18 | Reset attempts sau login thành công         | D1 valid email        | P1 correct pass | A3 (2 lần sai trước) | Thành công, bộ đếm về 0                        | Bộ đếm reset về 0, không bị khóa ngay khi nhập sai lại                              | Pass      |
| TC-19 | Gửi request cập nhật profile với role=admin | D1 valid email        | P1 correct pass | -                    | Backend bỏ qua trường role, không cho đổi role | Cập nhật và đăng nhập admin thành công bằng tài khoản user sau khi cập nhật         | Fail      |
