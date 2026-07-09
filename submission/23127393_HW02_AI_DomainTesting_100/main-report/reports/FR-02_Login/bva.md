# FR-02: Login and Account Lockout — Boundary Value Analysis Report

## 1. Feature Overview

| Item         | Detail                        |
| ------------ | ----------------------------- |
| Feature ID   | FR-02                         |
| Feature Name | Login and Account Lockout     |
| URL          | http://localhost:5173/login   |
| Technique    | Boundary Value Analysis (BVA) |

---

## 2. Xác định các biên (Boundaries)

FR-02 có các ràng buộc sau từ spec:

| Biến             | Ràng buộc                                                | Nguồn    |
| ---------------- | -------------------------------------------------------- | -------- |
| email            | Phải đúng format HTML5 (type="email")                    | README   |
| password         | Không rỗng                                               | Observed |
| login_attempts   | Sai < 3 lần → cho thử lại; sai ≥ 3 lần → lockout 30 giây | README   |
| lockout_duration | Khóa đúng 30 giây                                        | README   |

---

## 3. Phân tích biên từng biến

### 3.1 Biến login_attempts — Ngưỡng lockout

**Đây là biên quan trọng nhất của FR-02.**

| Điểm biên           | Giá trị    | Tên                  | Kết quả mong đợi          |
| ------------------- | ---------- | -------------------- | ------------------------- |
| Below lower bound   | 0 lần sai  | Không có lần sai nào | Cho đăng nhập bình thường |
| In-range            | 1 lần sai  | Trong miền valid     | Báo lỗi, cho thử lại      |
| Just below boundary | 2 lần sai  | Dưới biên lockout    | Báo lỗi, vẫn cho thử lại  |
| On-point (boundary) | 3 lần sai  | Đúng ngưỡng lockout  | Kích hoạt lockout 30 giây |
| Just above boundary | 4 lần sai  | Vượt biên            | Vẫn bị khóa               |
| Far above boundary  | 10 lần sai | Jauh di atas batas   | Vẫn bị khóa               |

---

### 3.2 Biến lockout_duration — Thời gian khóa 30 giây

| Điểm biên           | Giá trị             | Kết quả mong đợi                  |
| ------------------- | ------------------- | --------------------------------- |
| Just below boundary | 29 giây sau lockout | Vẫn bị khóa, không đăng nhập được |
| On-point (boundary) | 30 giây sau lockout | Tài khoản được mở khóa            |
| Just above boundary | 31 giây sau lockout | Đăng nhập thành công              |

---

### 3.3 Biến email — Format HTML5

**Email hợp lệ theo HTML5 phải có dạng: local@domain.tld**

| Điểm biên                     | Giá trị        | Kết quả mong đợi     |
| ----------------------------- | -------------- | -------------------- |
| Invalid — thiếu @             | usergmail.com  | HTML5 báo sai format |
| Invalid — thiếu local part    | @gmail.com     | HTML5 báo sai format |
| Invalid — thiếu domain        | user@          | HTML5 báo sai format |
| Valid — đủ cấu trúc tối thiểu | a@b.c          | Hợp lệ về format     |
| Valid — format đầy đủ         | user@gmail.com | Hợp lệ về format     |
| Edge — rỗng                   | ``             | Báo bắt buộc nhập    |

---

### 3.4 Biến password — Độ dài

> **Nhận xét:** Spec không nêu rõ giới hạn độ dài password khi đăng nhập (chỉ check khi đăng ký). BVA ở đây tập trung vào rỗng vs không rỗng.

| Điểm biên        | Giá trị       | Kết quả mong đợi                       |
| ---------------- | ------------- | -------------------------------------- |
| On-point rỗng    | `` (0 ký tự)  | Báo bắt buộc nhập password             |
| Just above empty | 1 ký tự `a`   | Cho phép submit (validate phía server) |
| Typical valid    | password đúng | Đăng nhập thành công                   |

---

## 4. Test Case Table

| TC ID  | Mô tả                                     | Input                                  | Biên liên quan                    | Kết quả mong đợi                                      | Kết quả thực tế                                           | Pass/Fail |
| ------ | ----------------------------------------- | -------------------------------------- | --------------------------------- | ----------------------------------------------------- | --------------------------------------------------------- | --------- |
| BVA-01 | Không có lần sai nào, login đúng          | email đúng, pass đúng, 0 lần sai trước | Below lower bound (attempts=0)    | Đăng nhập thành công                                  | Đăng nhập thành công                                      | Pass      |
| BVA-02 | Sai 1 lần                                 | email đúng, pass sai × 1               | In-range (attempts=1)             | Báo lỗi, cho thử lại                                  | Thử lại và đăng nhập thành công                           | Pass      |
| BVA-03 | Sai 2 lần — dưới biên lockout             | email đúng, pass sai × 2               | Just below boundary (attempts=2)  | Báo lỗi, vẫn cho thử lại                              | Tài khoản bị khóa, không cho thử lại                      | Fail      |
| BVA-04 | Sai đúng 3 lần — kích hoạt lockout        | email đúng, pass sai × 3               | On-point (attempts=3)             | Tài khoản bị khóa 30 giây, hiển thị thông báo         | Vẫn bị khóa, không hiển thị thông báo khóa bao lâu        | Fail      |
| BVA-05 | Sai 4 lần — vượt biên                     | email đúng, pass sai × 4               | Just above boundary (attempts=4)  | Vẫn bị khóa                                           | Tài khoản vẫn bị khóa                                     | Pass      |
| BVA-06 | Login đúng sau 29 giây (vẫn còn locked)   | email đúng, pass đúng, sau 29s         | Just below time boundary (29s)    | Vẫn bị khóa, không vào được                           | Vẫn bị khóa, không vào được                               | Pass      |
| BVA-07 | Login đúng sau đúng 30 giây               | email đúng, pass đúng, sau 30s         | On-point time boundary (30s)      | Đăng nhập thành công                                  | Tài khoản vẫn bị khóa, đăng nhập thất bại                 | Fail      |
| BVA-08 | Login đúng sau 31 giây                    | email đúng, pass đúng, sau 31s         | Just above time boundary (31s)    | Đăng nhập thành công                                  | Tài khoản vẫn bị khóa, đăng nhập thất bại                 | Fail      |
| BVA-09 | Email rỗng                                | email="", pass đúng                    | On-point empty email              | Báo bắt buộc nhập email                               | Hiển thị thông báo trường dữ liệu bắt buộc nhập           | Pass      |
| BVA-10 | Email format tối thiểu hợp lệ             | email=a@b.c, pass đúng                 | On-point minimum valid email      | Cho submit (server validate tồn tại)                  | Cho phép submit, thông báo đăng nhập thất bại             | Pass      |
| BVA-11 | Email thiếu @                             | email=usergmail.com                    | Invalid email boundary            | HTML5 báo sai format                                  | Chỉ hiển thị thông báo "Đăng nhập thất bại"               | Fail      |
| BVA-12 | Email thiếu local part                    | email=@gmail.com                       | Invalid email boundary            | HTML5 báo sai format                                  | Chỉ hiển thị thông báo "Đăng nhập thất bại"               | Fail      |
| BVA-13 | Email thiếu domain                        | email=user@                            | Invalid email boundary            | HTML5 báo sai format                                  | Chỉ hiển thị thông báo "Đăng nhập thất bại"               | Fail      |
| BVA-14 | Password rỗng                             | email đúng, pass=""                    | On-point empty password (0 ký tự) | Báo bắt buộc nhập password                            | Hiển thị thông báo trường dữ liệu bắt buộc nhập           | Pass      |
| BVA-15 | Password 1 ký tự                          | email đúng, pass=a                     | Just above empty (1 ký tự)        | Cho submit, server báo sai thông tin đăng nhập        | Cho phép submit, thông báo đăng nhập thất bại             | Pass      |
| BVA-16 | Login đúng sau lockout, bộ đếm reset về 0 | email đúng, pass đúng, sau 30s         | Post-lockout reset                | Thành công, bộ đếm về 0 (sai tiếp không bị khóa ngay) | Login thành công sau lockout, sai tiếp không bị khóa ngay | Pass      |

---
