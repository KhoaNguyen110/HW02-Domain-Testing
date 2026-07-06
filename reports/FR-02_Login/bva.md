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

| TC ID  | Mô tả                                     | Input                                  | Biên liên quan                    | Kết quả mong đợi                                      | Kết quả thực tế | Pass/Fail |
| ------ | ----------------------------------------- | -------------------------------------- | --------------------------------- | ----------------------------------------------------- | --------------- | --------- |
| BVA-01 | Không có lần sai nào, login đúng          | email đúng, pass đúng, 0 lần sai trước | Below lower bound (attempts=0)    | Đăng nhập thành công                                  |                 |           |
| BVA-02 | Sai 1 lần                                 | email đúng, pass sai × 1               | In-range (attempts=1)             | Báo lỗi, cho thử lại                                  |                 |           |
| BVA-03 | Sai 2 lần — dưới biên lockout             | email đúng, pass sai × 2               | Just below boundary (attempts=2)  | Báo lỗi, vẫn cho thử lại                              |                 |           |
| BVA-04 | Sai đúng 3 lần — kích hoạt lockout        | email đúng, pass sai × 3               | On-point (attempts=3)             | Tài khoản bị khóa 30 giây, hiển thị thông báo         |                 |           |
| BVA-05 | Sai 4 lần — vượt biên                     | email đúng, pass sai × 4               | Just above boundary (attempts=4)  | Vẫn bị khóa                                           |                 |           |
| BVA-06 | Login đúng sau 29 giây (vẫn còn locked)   | email đúng, pass đúng, sau 29s         | Just below time boundary (29s)    | Vẫn không vào được                                    |                 |           |
| BVA-07 | Login đúng sau đúng 30 giây               | email đúng, pass đúng, sau 30s         | On-point time boundary (30s)      | Đăng nhập thành công                                  |                 |           |
| BVA-08 | Login đúng sau 31 giây                    | email đúng, pass đúng, sau 31s         | Just above time boundary (31s)    | Đăng nhập thành công                                  |                 |           |
| BVA-09 | Email rỗng                                | email="", pass đúng                    | On-point empty email              | Báo bắt buộc nhập email                               |                 |           |
| BVA-10 | Email format tối thiểu hợp lệ             | email=a@b.c, pass đúng                 | On-point minimum valid email      | Cho submit (server validate tồn tại)                  |                 |           |
| BVA-11 | Email thiếu @                             | email=usergmail.com                    | Invalid email boundary            | HTML5 báo sai format                                  |                 |           |
| BVA-12 | Email thiếu local part                    | email=@gmail.com                       | Invalid email boundary            | HTML5 báo sai format                                  |                 |           |
| BVA-13 | Email thiếu domain                        | email=user@                            | Invalid email boundary            | HTML5 báo sai format                                  |                 |           |
| BVA-14 | Password rỗng                             | email đúng, pass=""                    | On-point empty password (0 ký tự) | Báo bắt buộc nhập password                            |                 |           |
| BVA-15 | Password 1 ký tự                          | email đúng, pass=a                     | Just above empty (1 ký tự)        | Cho submit, server báo sai password                   |                 |           |
| BVA-16 | Login đúng sau lockout, bộ đếm reset về 0 | email đúng, pass đúng, sau 30s         | Post-lockout reset                | Thành công, bộ đếm về 0 (sai tiếp không bị khóa ngay) |                 |           |

---

## 5. Bug Reports (phát hiện qua BVA)

### BUG-02 (confirmed): Không hiển thị thông báo lockout tại on-point (attempts=3)

| Field         | Detail                                                                                            |
| ------------- | ------------------------------------------------------------------------------------------------- |
| Bug ID        | BUG-02                                                                                            |
| Phát hiện tại | BVA-04                                                                                            |
| Severity      | Major                                                                                             |
| Priority      | High                                                                                              |
| Summary       | Tại đúng ngưỡng biên (3 lần sai), hệ thống kích hoạt lockout nhưng không thông báo cho người dùng |
| Expected      | Hiển thị "Tài khoản bị tạm khóa X giây" khi đạt on-point                                          |
| Actual        | Silent lockout — người dùng không nhận được feedback gì                                           |

### BUG-03 (cần verify): Không rõ behavior tại đúng boundary 30 giây

| Field         | Detail                                                                                            |
| ------------- | ------------------------------------------------------------------------------------------------- |
| Bug ID        | BUG-03                                                                                            |
| Phát hiện tại | BVA-07                                                                                            |
| Severity      | Minor                                                                                             |
| Priority      | Medium                                                                                            |
| Summary       | Cần xác minh behavior tại đúng on-point 30 giây — hệ thống có unlock chính xác không hay cần 31s+ |
| Steps         | 1. Trigger lockout <br> 2. Đợi đúng 30 giây <br> 3. Login ngay                                    |
| Expected      | Đăng nhập thành công tại đúng 30 giây                                                             |
| Actual        | Chưa verify                                                                                       |

---

## 6. AI Gap Analysis

| Gap                          | Mô tả                                     | Lý do AI bỏ sót                                          |
| ---------------------------- | ----------------------------------------- | -------------------------------------------------------- |
| BVA-06/07/08 (time boundary) | Test chính xác tại 29s, 30s, 31s          | AI không thể đo thời gian thực — cần human test thủ công |
| BUG-03 (off-by-one ở time)   | Có thể hệ thống dùng > 30 thay vì >= 30   | AI không đọc được source code backend                    |
| BVA-16 (reset bộ đếm)        | Sau login thành công bộ đếm có về 0 không | Logic ẩn, AI không suy luận nếu thiếu spec               |

---

## 7. Test Summary

| Metric               | Value                      |
| -------------------- | -------------------------- |
| Total BVA test cases | 16                         |
| Executed             |                            |
| Passed               |                            |
| Failed               |                            |
| Not executed         |                            |
| Bugs found           | 2 confirmed + 1 cần verify |

---

## 8. Recommendations

1. **Ưu tiên fix BUG-02** - Hiển thị thông báo lockout cho người dùng
2. **Verify BUG-03** - Kiểm tra behavior tại đúng 30 giây
3. **Bổ sung test tự động** cho các test case time-based
4. **Cập nhật spec** để làm rõ behavior tại boundary 30 giây và reset bộ đếm
5. **Thêm logging** để dễ dàng debug các vấn đề liên quan đến lockout

---

**Report Date:**
**Prepared by:**
**Reviewed by:**
