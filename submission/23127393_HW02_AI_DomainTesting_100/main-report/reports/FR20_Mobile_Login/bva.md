# Pool D — Mobile Login — Boundary Value Analysis Report

## 1. Feature Overview

| Item         | Detail                        |
| ------------ | ----------------------------- |
| Feature ID   | Pool D — Mobile Login         |
| Feature Name | Đăng nhập trên Mobile App     |
| Platform     | React Native (Expo Go)        |
| Technique    | Boundary Value Analysis (BVA) |

---

## 2. Xác định các biên (Boundaries)

| Biến               | Ràng buộc                               | Nguồn       |
| ------------------ | --------------------------------------- | ----------- |
| `email`            | Không được rỗng                         | Observed    |
| `password`         | Không được rỗng, `secureTextEntry=true` | Source code |
| `login_attempts`   | Sai ≥ 3 lần → lockout 30 giây           | README spec |
| `lockout_duration` | 30 giây                                 | README spec |

---

## 3. Phân tích biên từng biến

### 3.1 Biến `login_attempts` — Ngưỡng lockout

| Điểm biên           | Giá trị       | Tên                     | Kết quả mong đợi              |
| ------------------- | ------------- | ----------------------- | ----------------------------- |
| In-range            | 1 lần sai     | Dưới ngưỡng             | Báo lỗi, cho thử lại          |
| Just below boundary | 2 lần sai     | Dưới biên               | Vẫn cho thử lại               |
| **On-point**        | **3 lần sai** | **Đúng ngưỡng lockout** | **Kích hoạt lockout 30 giây** |
| Just above boundary | 4 lần sai     | Vượt biên               | Vẫn bị khóa                   |

---

### 3.2 Biến `lockout_duration` — Thời gian khóa 30 giây

| Điểm biên           | Giá trị     | Kết quả mong đợi           |
| ------------------- | ----------- | -------------------------- |
| Just below boundary | 29 giây     | Vẫn bị khóa                |
| **On-point**        | **30 giây** | **Tài khoản được mở khóa** |
| Just above boundary | 31 giây     | Đăng nhập thành công       |

---

### 3.3 Biến `email` — Rỗng và không rỗng

| Điểm biên    | Giá trị        | Kết quả mong đợi             |
| ------------ | -------------- | ---------------------------- |
| **On-point** | `""` (0 ký tự) | Báo bắt buộc nhập email      |
| Just above   | 1 ký tự `"a"`  | Cho submit (server validate) |

---

### 3.4 Biến `password` — Rỗng và không rỗng

| Điểm biên    | Giá trị        | Kết quả mong đợi             |
| ------------ | -------------- | ---------------------------- |
| **On-point** | `""` (0 ký tự) | Báo bắt buộc nhập password   |
| Just above   | 1 ký tự `"a"`  | Cho submit (server validate) |

---

## 4. Test Case Table

| TC ID  | Mô tả                               | Input                                      | Biên liên quan                | Kết quả mong đợi                            | Kết quả thực tế                                                                                                                           | Pass/Fail |
| ------ | ----------------------------------- | ------------------------------------------ | ----------------------------- | ------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | --------- |
| BVA-01 | Sai 1 lần — trong miền valid        | email đúng, pass sai × 1                   | In-range (attempts=1)         | Báo lỗi, cho thử lại                        | Báo lỗi, cho thử lại                                                                                                                      | Pass      |
| BVA-02 | Sai 2 lần — just below boundary     | email đúng, pass sai × 2                   | Just below (attempts=2)       | Vẫn cho thử lại                             | Báo lỗi, khóa tài khoản, không cho phép đăng nhập                                                                                         | Fail      |
| BVA-03 | Sai đúng 3 lần — on-point lockout   | email đúng, pass sai × 3                   | **On-point (attempts=3)**     | Lockout 30 giây, hiển thị thông báo rõ ràng | Tài khoản bị khóa nhưng không thông báo rõ ràng cho người dùng                                                                            | Fail      |
| BVA-04 | Sai 4 lần — just above boundary     | email đúng, pass sai × 4                   | Just above (attempts=4)       | Vẫn bị khóa                                 |                                                                                                                                           |           |
| BVA-05 | Đăng nhập đúng sau 29 giây          | email đúng, pass đúng, sau 29s             | Just below time (29s)         | Vẫn không vào được                          | Tài khoản vẫn bị khóa                                                                                                                     | Pass      |
| BVA-06 | Đăng nhập đúng sau đúng 30 giây     | email đúng, pass đúng, sau 30s             | **On-point time (30s)**       | Đăng nhập thành công                        | Đăng nhập thất bại vì tài khoản vẫn bị khóa                                                                                               | Fail      |
| BVA-07 | Đăng nhập đúng sau 31 giây          | email đúng, pass đúng, sau 31s             | Just above time (31s)         | Đăng nhập thành công                        | Đăng nhập thất bại vì tài khoản vẫn bị khóa                                                                                               | Fail      |
| BVA-08 | Email rỗng                          | `""` email                                 | **On-point email=0 ký tự**    | Báo bắt buộc nhập email                     | Không thông báo trường dữ liệu bắt buộc phải nhập cho người dùng                                                                          | Fail      |
| BVA-09 | Email 1 ký tự                       | `"a"` email                                | Just above email=1 ký tự      | Cho submit, server báo sai format           | Cho phép submit, không thông báo sai format                                                                                               | Fail      |
| BVA-10 | Password rỗng                       | email đúng, `""` password                  | **On-point password=0 ký tự** | Báo bắt buộc nhập password                  | Không thông báo trường dữ liệu bắt buộc phải nhập cho người dùng, , cho phép submit khi password là chuỗi rỗng và tính login_attempts = 1 | Fail      |
| BVA-11 | Password 1 ký tự                    | `"a"` password                             | Just above password=1 ký tự   | Cho submit, server báo "Đăng nhập thất bại" | Cho phép submit, server báo "Đăng nhập thất bại"                                                                                          | Pass      |
| BVA-12 | Reset attempts sau login thành công | Nhập sai 2 lần → đăng nhập đúng → sai tiếp | Post-success reset            | Bộ đếm về 0, không bị lockout sớm           | Bộ đếm được reset sau khi đăng nhập đúng, không bị lockout sớm nếu sai tiếp                                                               | Pass      |

---

## 5. Bug Reports (phát hiện qua BVA)

### BUG-03: Lockout tại điểm dưới biên

| Field             | Detail                                                            |
| ----------------- | ----------------------------------------------------------------- |
| **Bug ID**        | BUG-03                                                            |
| **Phát hiện tại** | BVA-03                                                            |
| **Severity**      | Major                                                             |
| **Priority**      | High                                                              |
| **Summary**       | Tại điểm dưới biên (login_attempts = 2 < 3), tài khoản đã bị khóa |
| **Expected**      | Tài khoản bị khóa sau 3 lần đăng nhập sai                         |
| **Actual**        | Sau chỉ mới 2 lần đăng nhập sai, tài khoản đã bị khóa             |

### BUG-04: Thời gian lockout thực tế trên mobile

| Field             | Detail                                                                                  |
| ----------------- | --------------------------------------------------------------------------------------- |
| **Bug ID**        | BUG-04                                                                                  |
| **Phát hiện tại** | BVA-05, BVA-06                                                                          |
| **Severity**      | Major                                                                                   |
| **Priority**      | High                                                                                    |
| **Summary**       | Web app lockout thực tế là 3 phút thay vì 30 giây                                       |
| **Expected**      | Mở khóa sau đúng 30 giây                                                                |
| **Actual**        | Người dùng đợi 3 phút mới được mở khóa, hệ thống không thông báo rõ ràng tới người dùng |

---

## 6. AI Gap Analysis

| Gap                       | Mô tả                                   | Lý do AI bỏ sót                                               |
| ------------------------- | --------------------------------------- | ------------------------------------------------------------- |
| BVA-05/06/07 (timing)     | Test chính xác 29s/30s/31s              | AI không thể đo thời gian thực trên thiết bị mobile           |
| BUG-04 (lockout duration) | Mobile có cùng bug timing như web không | AI không chạy app, không biết backend có phân biệt web/mobile |
| BUG-02 (keyboard type)    | Bàn phím sai loại                       | Đặc thù UX mobile, AI cần đọc source code mới phát hiện       |

---

## 7. Test Summary

| Metric               | Value |
| -------------------- | ----- |
| Total BVA test cases | 12    |
| Executed             | 12    |
| Passed               | 4     |
| Failed               | 8     |
| Not executed         | 0     |
| Bugs found           | 2     |
