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

| TC ID | Mô tả                                 | Email (Miền)          | Password (Miền) | Attempts             | Kết quả mong đợi                               | Kết quả thực tế | Pass/Fail |
| ----- | ------------------------------------- | --------------------- | --------------- | -------------------- | ---------------------------------------------- | --------------- | --------- |
| TC-01 | Đăng nhập thành công                  | D1 valid email        | P1 correct pass | A1 (0)               | Redirect về /, nhận JWT Token                  |                 |           |
| TC-02 | Email rỗng                            | D6 empty              | P1 correct pass | A1 (0)               | Báo bắt buộc nhập email                        |                 |           |
| TC-03 | Password rỗng                         | D1 valid email        | P3 empty        | A1 (0)               | Báo bắt buộc nhập password                     |                 |           |
| TC-04 | Cả 2 field rỗng                       | D6 empty              | P3 empty        | A1 (0)               | Báo bắt buộc nhập                              |                 |           |
| TC-05 | Email sai format (không có @)         | D2 userexample.com    | P1 correct pass | A1 (0)               | HTML5 validation báo sai format                |                 |           |
| TC-06 | Email sai format (thiếu domain)       | D3 user@              | P1 correct pass | A1 (0)               | HTML5 validation báo sai format                |                 |           |
| TC-07 | Email chỉ có @                        | D4 @gmail.com         | P1 correct pass | A1 (0)               | HTML5 validation báo sai format                |                 |           |
| TC-08 | Email không tồn tại                   | D5 notexist@gmail.com | P2 wrong pass   | A1 (0)               | Báo đăng nhập thất bại                         |                 |           |
| TC-09 | Sai password lần 1                    | D1 valid email        | P2 wrong pass   | A2 (1)               | Báo đăng nhập thất bại, cho thử lại            |                 |           |
| TC-10 | Sai password lần 2                    | D1 valid email        | P2 wrong pass   | A3 (2)               | Báo đăng nhập thất bại, cho thử lại            |                 |           |
| TC-11 | Sai password lần 3 — trigger lockout  | D1 valid email        | P2 wrong pass   | A4 (3)               | Tài khoản bị khóa 30 giây, hiển thị thông báo  |                 |           |
| TC-12 | Login đúng pass khi đang bị lockout   | D1 valid email        | P1 correct pass | A4 (đang locked)     | Vẫn không vào được, hiển thị thông báo bị khóa |                 |           |
| TC-13 | Login sai tiếp khi đang bị lockout    | D1 valid email        | P2 wrong pass   | A5 (4+)              | Vẫn bị khóa                                    |                 |           |
| TC-14 | Login đúng sau khi hết lockout (>30s) | D1 valid email        | P1 correct pass | A6 (sau 30s)         | Đăng nhập thành công, redirect về /            |                 |           |
| TC-15 | Password có khoảng trắng              | D1 valid email        | P4 ` pass`      | A1 (0)               | Xác định hệ thống có trim không                |                 |           |
| TC-16 | Email chữ HOA                         | D8 USER@GMAIL.COM     | P1 correct pass | A1 (0)               | Đăng nhập thành công (case-insensitive)        |                 |           |
| TC-17 | Password đúng nhưng khác hoa/thường   | D1 valid email        | P5 CORRECTPASS  | A1 (0)               | Đăng nhập thất bại (case-sensitive)            |                 |           |
| TC-18 | Reset attempts sau login thành công   | D1 valid email        | P1 correct pass | A3 (2 lần sai trước) | Thành công, bộ đếm về 0                        |                 |           |

## 4. Bug Reports

### BUG-01: Tiêu đề trang Login hiển thị sai

| Field              | Detail                                                     |
| ------------------ | ---------------------------------------------------------- |
| Bug ID             | BUG-01                                                     |
| Severity           | Minor                                                      |
| Priority           | Low                                                        |
| Feature            | FR-02                                                      |
| Summary            | Tiêu đề trang Login hiển thị "Đăng kí" thay vì "Đăng nhập" |
| Steps to Reproduce | 1. Vào http://localhost:5173/login                         |
| Expected           | Tiêu đề hiển thị "Đăng nhập"                               |
| Actual             | Tiêu đề hiển thị "Đăng kí"                                 |
| Screenshot         | screenshots/bug-01-wrong-title.png                         |

### BUG-02: Không hiển thị thông báo khi tài khoản bị khóa

| Field              | Detail                                                                                                                  |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------- |
| Bug ID             | BUG-02                                                                                                                  |
| Severity           | Major                                                                                                                   |
| Priority           | High                                                                                                                    |
| Feature            | FR-02                                                                                                                   |
| Summary            | Sau 3 lần đăng nhập sai, tài khoản bị khóa nhưng không hiển thị thông báo, không có bộ đếm ngược thời gian              |
| Steps to Reproduce | 1. Vào http://localhost:5173/login <br> 2. Nhập đúng email, sai password 3 lần liên tiếp <br> 3. Thử nhập đúng password |
| Expected           | Hiển thị thông báo "Tài khoản bị tạm khóa, vui lòng thử lại sau X giây"                                                 |
| Actual             | Không có thông báo, người dùng không biết lý do tại sao không đăng nhập được dù nhập đúng thông tin                     |
| Screenshot         | screenshots/bug-02-no-lockout-message.png                                                                               |

## 5. AI Gap Analysis

### Những gì AI có thể bỏ sót

| Gap                              | Mô tả                                      | Lý do AI bỏ sót                                                                                      |
| -------------------------------- | ------------------------------------------ | ---------------------------------------------------------------------------------------------------- |
| BUG-01 (sai tiêu đề)             | Tiêu đề "Đăng kí" thay vì "Đăng nhập"      | AI không thực sự chạy app, chỉ phân tích logic — không thể phát hiện lỗi UI visual                   |
| BUG-02 (thiếu thông báo lockout) | Lockout xảy ra nhưng silent                | AI sinh test case dựa trên spec — nếu spec nói "có lockout" thì AI assume là đúng, không kiểm tra UX |
| TC-15 (trim khoảng trắng)        | Behavior với password có space             | AI không biết implementation detail của backend                                                      |
| TC-18 (reset bộ đếm)             | Bộ đếm có reset sau login thành công không | Logic nghiệp vụ ẩn, AI không suy luận được nếu không có spec rõ ràng                                 |

### Lý do chính AI bỏ sót

1. **AI không chạy app thực tế** → không thể phát hiện lỗi UI, lỗi UX
2. **AI tin tưởng spec 100%** → nếu spec ghi "có thông báo lockout" thì AI assume đúng
3. **Prompt chưa yêu cầu kiểm tra UX/visual** → cần prompt thêm "check UI labels, messages"

## 6. Test Summary

| Metric           | Value              |
| ---------------- | ------------------ |
| Total test cases | 18                 |
| Executed         |                    |
| Passed           |                    |
| Failed           |                    |
| Not executed     |                    |
| Bugs found       | 2 (BUG-01, BUG-02) |

## 7. Cross-Reference với BVA Report

| BVA Test Case     | Domain Test Case       | Liên quan                          |
| ----------------- | ---------------------- | ---------------------------------- |
| BVA-01 đến BVA-05 | TC-09 đến TC-13        | Kiểm thử login_attempts boundary   |
| BVA-06 đến BVA-08 | TC-14                  | Kiểm thử lockout_duration boundary |
| BVA-09 đến BVA-13 | TC-02, TC-05 đến TC-07 | Kiểm thử email validation          |
| BVA-14 đến BVA-15 | TC-03, TC-04           | Kiểm thử password validation       |
| BVA-16            | TC-18                  | Kiểm thử reset bộ đếm              |

## 8. Recommendations

1. **Fix BUG-01** - Sửa tiêu đề trang từ "Đăng kí" thành "Đăng nhập"
2. **Fix BUG-02 (critical)** - Thêm thông báo lockout và bộ đếm ngược thời gian
3. **Xác định rõ behavior** với:
   - Trim khoảng trắng trong password (TC-15)
   - Case-sensitive của password (TC-17)
   - Reset bộ đếm sau login thành công (TC-18)
4. **Bổ sung test cases** cho các edge cases phát hiện:
   - Email có dấu tiếng Việt
   - Password đặc biệt (ký tự đặc biệt, Unicode)
   - Concurrent login attempts từ nhiều tab/browser

**Report Date:**
**Prepared by:**
**Reviewed by:**
