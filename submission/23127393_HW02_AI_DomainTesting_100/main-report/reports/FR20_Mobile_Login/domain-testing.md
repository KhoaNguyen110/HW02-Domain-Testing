# Pool D — Mobile Login — Domain Testing Report

## 1. Feature Overview

| Item         | Detail                    |
| ------------ | ------------------------- |
| Feature ID   | Pool D — Mobile Login     |
| Feature Name | Đăng nhập trên Mobile App |
| Platform     | React Native (Expo Go)    |
| Technique    | Domain Testing            |

### Mô tả

Người dùng nhập email và mật khẩu để đăng nhập trên mobile app. Sau đăng nhập thành công nhận JWT Token, redirect về trang chủ. Sai từ 3 lần trở lên bị lockout 30 giây.

---

## 2. Domain Testing — Step-by-step

### Bước 1: Xác định biến input

| Biến             | Kiểu    | Mô tả                          |
| ---------------- | ------- | ------------------------------ |
| `email`          | String  | Địa chỉ email đăng nhập        |
| `password`       | String  | Mật khẩu đăng nhập             |
| `login_attempts` | Integer | Số lần đăng nhập sai liên tiếp |

---

### Bước 2: Phân vùng miền (Domain Partitioning)

#### Biến `email`

| Miền | Loại    | Điều kiện                           | Ví dụ                |
| ---- | ------- | ----------------------------------- | -------------------- |
| E1   | Valid   | Email đúng format, tồn tại trong DB | `user@gmail.com`     |
| E2   | Invalid | Sai format — không có `@`           | `usergmail.com`      |
| E3   | Invalid | Sai format — thiếu domain           | `user@`              |
| E4   | Invalid | Đúng format nhưng không tồn tại     | `notexist@gmail.com` |
| E5   | Edge    | Rỗng                                | `""`                 |
| E6   | Edge    | Chỉ có khoảng trắng                 | `"   "`              |
| E7   | Edge    | Email chữ HOA                       | `USER@GMAIL.COM`     |

#### Biến `password`

| Miền | Loại    | Điều kiện                           | Ví dụ             |
| ---- | ------- | ----------------------------------- | ----------------- |
| P1   | Valid   | Đúng password của tài khoản         | `correctPass123!` |
| P2   | Invalid | Sai password                        | `wrongPass`       |
| P3   | Edge    | Rỗng                                | `""`              |
| P4   | Edge    | Password đúng nhưng khác hoa/thường | `CORRECTPASS123!` |

#### Biến `login_attempts`

| Miền | Loại         | Giá trị     | Hành vi mong đợi          |
| ---- | ------------ | ----------- | ------------------------- |
| A1   | Valid        | 0 lần sai   | Cho đăng nhập bình thường |
| A2   | Valid        | 1-2 lần sai | Báo lỗi, cho thử lại      |
| A3   | Boundary     | 3 lần sai   | Lockout 30 giây           |
| A4   | Invalid      | 4+ lần sai  | Vẫn bị khóa               |
| A5   | Post-lockout | Sau 30 giây | Cho đăng nhập lại         |

---

### Bước 3: Chọn test case đại diện

---

## 3. Test Case Table

| TC ID | Mô tả                                | Email                   | Password         | Attempts     | Kết quả mong đợi                                 | Kết quả thực tế                                                                                          | Pass/Fail |
| ----- | ------------------------------------ | ----------------------- | ---------------- | ------------ | ------------------------------------------------ | -------------------------------------------------------------------------------------------------------- | --------- | --- | --- |
| TC-01 | Đăng nhập thành công                 | E1 valid                | P1 correct       | A1 (0)       | Redirect về home, hiển thị tên user trên navbar  | Đăng nhập thành công, điều hướng trang chính xác                                                         | Pass      |
| TC-02 | Email rỗng                           | E5 empty                | P1 correct       | A1 (0)       | Báo lỗi bắt buộc nhập email                      | Không thông báo trường dữ liệu bắt buộc nhập cho người dùng, chỉ thông báo "Đăng nhập thất bại"          | Fail      |
| TC-03 | Password rỗng                        | E1 valid                | P3 empty         | A1 (0)       | Báo lỗi bắt buộc nhập password                   | Không báo lỗi buộc nhập, cho phép submit khi pass rỗng và tính login_attempts = 1                        |           |
| TC-04 | Cả 2 rỗng                            | E5 empty                | P3 empty         | A1 (0)       | Báo lỗi bắt buộc nhập                            | Không thông báo trường dữ liệu bắt buộc nhập cho người dùng, chỉ thông báo "Đăng nhập thất bại"          | Fail      |
| TC-05 | Email sai format (không có @)        | E2 `usergmail.com`      | P1 correct       | A1 (0)       | Báo sai format email                             | Không thông báo trường dữ liệu bắt buộc nhập cho người dùng, chỉ thông báo "Đăng nhập thất bại"          | Fail      |
| TC-06 | Email không tồn tại                  | E4 `notexist@gmail.com` | P2 wrong         | A1 (0)       | Báo đăng nhập thất bại                           | Thông báo "Đăng nhập thất bại"                                                                           | Pass      |
| TC-07 | Sai password lần 1                   | E1 valid                | P2 wrong         | A2 (1)       | Báo "Đăng nhập thất bại. Vui lòng kiểm tra lại." | Thông báo "Đăng nhập thất bại", cho phép thử lại                                                         | Pass      |
| TC-08 | Sai password lần 2                   | E1 valid                | P2 wrong         | A2 (2)       | Báo lỗi, cho thử lại                             | Báo lỗi, khóa tài khoản                                                                                  | Fail      |
| TC-09 | Sai password lần 3 — trigger lockout | E1 valid                | P2 wrong         | A3 (3)       | Thông báo tài khoản bị khóa 30 giây              | Khóa tài khoản sau 3 lần sai                                                                             | Pass      |
| TC-10 | Đăng nhập đúng khi đang bị lockout   | E1 valid                | P1 correct       | A3 (locked)  | Vẫn không vào được, hiển thị thông báo bị khóa   | Không đăng nhập được nhưng người dùng không biết tại sao vì không có thông báo tài khoản bị khóa rõ ràng | Fail      |
| TC-11 | Đăng nhập đúng sau lockout (>30s)    | E1 valid                | P1 correct       | A5 (sau 30s) | Đăng nhập thành công                             | Vẫn bị khóa, người dùng tiếp tục chờ đợi                                                                 | Fail      |
| TC-12 | Email chữ HOA                        | E7 `USER@GMAIL.COM`     | P1 correct       | A1 (0)       | Đăng nhập thành công (case-insensitive)          | Đăng nhập thất bại                                                                                       | Fail      |
| TC-13 | Password đúng nhưng khác hoa/thường  | E1 valid                | P4 `CORRECTPASS` | A1 (0)       | Đăng nhập thất bại (case-sensitive)              | Đăng nhập thất bại                                                                                       | Pass      |     |     |
| TC-14 | Bàn phím khi nhập email              | E1 valid                | -                | -            | Bàn phím loại email (có phím @ nổi bật)          | Bàn phím loại thường                                                                                     | Fail      |
| TC-15 | Password được ẩn bằng ký tự đặc biệt | E1 valid                | P1 correct       | A1 (0)       | Ký tự bị ẩn khi nhập password                    | Password bị ẩn bằng kí tự đặc biệt                                                                       | Pass      |

---

## 4. Bug Reports

## 4. Bug Reports

### BUG-01: Label field email hiển thị "Username" thay vì "Email"

| Field             | Detail                                                    |
| ----------------- | --------------------------------------------------------- |
| **Bug ID**        | BUG-01                                                    |
| **Phát hiện tại** | Domain Testing — TC-14                                    |
| **Severity**      | Minor                                                     |
| **Priority**      | Low                                                       |
| **Summary**       | Label của field email hiển thị "Username" thay vì "Email" |
| **Expected**      | Label hiển thị "Email"                                    |
| **Actual**        | Label hiển thị "Username"                                 |
| **Screenshot**    | `screenshots/bug-01-wrong-label.png`                      |

---

### BUG-02: Không validate khi email/password rỗng

| Field             | Detail                                                                                                                                                                                                                                       |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bug ID**        | BUG-02                                                                                                                                                                                                                                       |
| **Phát hiện tại** | Domain Testing — TC-02, TC-03, TC-04                                                                                                                                                                                                         |
| **Severity**      | Major                                                                                                                                                                                                                                        |
| **Priority**      | High                                                                                                                                                                                                                                         |
| **Summary**       | Khi email hoặc password rỗng, app không báo lỗi bắt buộc nhập mà vẫn gửi request lên server và hiển thị "Đăng nhập thất bại" — gây nhầm lẫn cho người dùng. Đặc biệt TC-03: khi email chính xác và password rỗng còn tính vào login_attempts |
| **Expected**      | Báo lỗi "Vui lòng nhập email/mật khẩu" ngay trên client, không gửi request                                                                                                                                                                   |
| **Actual**        | Gửi request lên server, trả về "Đăng nhập thất bại", tính vào login_attempts khi email đúng và password rỗng                                                                                                                                 |

---

### BUG-03: Không validate format email

| Field             | Detail                                                                                                  |
| ----------------- | ------------------------------------------------------------------------------------------------------- |
| **Bug ID**        | BUG-03                                                                                                  |
| **Phát hiện tại** | Domain Testing — TC-05                                                                                  |
| **Severity**      | Minor                                                                                                   |
| **Priority**      | Medium                                                                                                  |
| **Summary**       | Email sai format (không có @) không bị báo lỗi format mà vẫn gửi request và trả về "Đăng nhập thất bại" |
| **Expected**      | Báo lỗi "Email không đúng định dạng"                                                                    |
| **Actual**        | Hiển thị "Đăng nhập thất bại"                                                                           |

---

### BUG-04: Lockout xảy ra sau 2 lần sai thay vì 3 lần

| Field             | Detail                                                                                        |
| ----------------- | --------------------------------------------------------------------------------------------- |
| **Bug ID**        | BUG-04                                                                                        |
| **Phát hiện tại** | Domain Testing — TC-08, BVA — BVA-02                                                          |
| **Severity**      | Major                                                                                         |
| **Priority**      | High                                                                                          |
| **Summary**       | Tài khoản bị khóa sau chỉ 2 lần đăng nhập sai — vi phạm spec quy định lockout sau ≥ 3 lần sai |
| **Expected**      | Lockout kích hoạt sau đúng 3 lần sai (on-point)                                               |
| **Actual**        | Lockout kích hoạt sau 2 lần sai (dưới biên)                                                   |
| **Screenshot**    | `screenshots/bug-04-early-lockout.png`                                                        |

---

### BUG-05: Silent lockout — không hiển thị thông báo bị khóa

| Field             | Detail                                                                                                                                                                          |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bug ID**        | BUG-05                                                                                                                                                                          |
| **Phát hiện tại** | Domain Testing — TC-09, TC-10, BVA — BVA-03                                                                                                                                     |
| **Severity**      | Major                                                                                                                                                                           |
| **Priority**      | High                                                                                                                                                                            |
| **Summary**       | Khi tài khoản bị lockout, app hiển thị cùng message "Đăng nhập thất bại" như lỗi sai password — người dùng không biết lý do tại sao không đăng nhập được dù nhập đúng thông tin |
| **Expected**      | Hiển thị "Tài khoản bị tạm khóa, vui lòng thử lại sau X giây"                                                                                                                   |
| **Actual**        | Hiển thị "Đăng nhập thất bại. Vui lòng kiểm tra lại." cho mọi loại lỗi                                                                                                          |

---

### BUG-06: Thời gian lockout thực tế ~3 phút thay vì 30 giây

| Field             | Detail                                                                                                                           |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| **Bug ID**        | BUG-06                                                                                                                           |
| **Phát hiện tại** | Domain Testing — TC-11, BVA — BVA-05, BVA-06                                                                                     |
| **Severity**      | Major                                                                                                                            |
| **Priority**      | High                                                                                                                             |
| **Summary**       | Spec quy định lockout 30 giây nhưng thực tế người dùng phải chờ ~3 phút mới mở khóa được, hệ thống không thông báo thời gian chờ |
| **Expected**      | Mở khóa sau đúng 30 giây                                                                                                         |
| **Actual**        | Vẫn bị khóa sau 30 giây, phải chờ ~3 phút                                                                                        |
| **Screenshot**    | `screenshots/bug-06-lockout-duration.png`                                                                                        |

---

### BUG-07: Email chữ HOA không đăng nhập được

| Field             | Detail                                                                                                                   |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------ |
| **Bug ID**        | BUG-07                                                                                                                   |
| **Phát hiện tại** | Domain Testing — TC-12                                                                                                   |
| **Severity**      | Minor                                                                                                                    |
| **Priority**      | Medium                                                                                                                   |
| **Summary**       | Nhập email chữ HOA (`USER@GMAIL.COM`) thất bại dù email đúng — hệ thống phân biệt hoa/thường với email, không đúng chuẩn |
| **Expected**      | Đăng nhập thành công (email nên case-insensitive theo chuẩn RFC)                                                         |
| **Actual**        | Đăng nhập thất bại                                                                                                       |

---

## 5. AI Gap Analysis

| Gap                     | Mô tả                             | Lý do AI bỏ sót                     |
| ----------------------- | --------------------------------- | ----------------------------------- |
| BUG-03 (lockout timing) | Thời gian lockout thực tế bao lâu | AI không chạy app thực tế để đo đạc |
| TC-11 (sau 30s)         | Test chính xác timing lockout     | AI không thể đo thời gian thực      |

---

## 6. Test Summary

| Metric           | Value |
| ---------------- | ----- |
| Total test cases | 15    |
| Executed         | 15    |
| Passed           | 5     |
| Failed           | 10    |
| Not executed     | 0     |
| Bugs found       | 3     |
