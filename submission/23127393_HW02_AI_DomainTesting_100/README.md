# HW02 — Domain Testing & Boundary Value Analysis

# Thông tin chung

| Item          | Detail            |
| ------------- | ----------------- |
| **Sinh viên** | Nguyễn Đăng Khoa  |
| **MSSV**      | 23127393          |
| **Môn học**   | Kiểm thử phần mềm |

## 1. Self-Assessment Table

| No.       | Criteria                                                  | Grade   | Self-Assessed Grade |
| --------- | --------------------------------------------------------- | ------- | ------------------- |
| 1         | Feature A — Login and Account Lockout (Domain + Boundary) | 25      | 25                  |
| 2         | Feature B — Checkout (Domain + Boundary)                  | 25      | 25                  |
| 3         | Feature C — Category Management (Domain + Boundary)       | 25      | 25                  |
| 4         | Feature D — Mobile Login (Domain + Boundary)              | 15      | 15                  |
| 5         | Agent Skills                                              | 10      | 10                  |
| **Total** |                                                           | **100** | **100**             |

---

## 2. Test Summary Report

### 2.1 Tổng hợp theo từng Feature

| Feature                       | Số test case thiết kế | Đã thực thi | Passed | Failed | Chưa thực thi | Bugs found |
| ----------------------------- | --------------------- | ----------- | ------ | ------ | ------------- | ---------- |
| A — Login and Account Lockout | 35                    | 35          | 18     | 17     | 0             | 8          |
| B — Checkout                  | 25                    | 24          | 11     | 13     | 1             | 4          |
| C — Category Management       | 27                    | 27          | 15     | 12     | 0             | 6          |
| D — Mobile Login              | 27                    | 27          | 9      | 18     | 0             | 7          |
| **Tổng cộng**                 | **114**               | **113**     | **53** | **60** | **1**         | **25**     |

### 2.2 Tổng quan chung

| Metric                          | Value                                                    |
| ------------------------------- | -------------------------------------------------------- |
| Số feature đã kiểm thử          | 4                                                        |
| Tổng số test case thiết kế      | 114 (Domain Testing + Boundary Value Analysis)           |
| Tổng số test case đã thực thi   | 113                                                      |
| Tổng số test case Passed        | 53                                                       |
| Tổng số test case Failed        | 60                                                       |
| Tổng số test case chưa thực thi | 1 (BVA-11 — stress test quantity=999 ở Feature Checkout) |
| Tổng số bug phát hiện           | 25                                                       |

### 2.3 Ghi chú kỹ thuật áp dụng

Mỗi feature được kiểm thử bằng 2 kỹ thuật kết hợp:

- **Domain Testing** (Equivalence Partitioning): phân vùng miền Valid/Invalid/Edge cho từng biến input, chọn test case đại diện cho mỗi miền.
- **Boundary Value Analysis (BVA)**: xác định các ràng buộc có giá trị biên rõ ràng (ngưỡng số học, thời gian, độ dài), thiết kế test case tại các điểm on-point/just-below/just-above boundary để phát hiện lỗi off-by-one và các sai lệch giá trị cấu hình.

Kết quả bug ở mỗi feature là tổng hợp từ cả 2 kỹ thuật sau khi đối chiếu và loại trùng — một số bug được cả Domain Testing và BVA cùng phát hiện (ví dụ: lockout sai ngưỡng, silent lockout, sai thời gian lockout), một số chỉ được BVA xác nhận chắc chắn (off-by-one errors), và một số chỉ Domain Testing phát hiện được (lỗi UI, lỗi logic nghiệp vụ rộng như trùng tên, phân quyền).

---

## 3. Demo Videos

- Video demo sử dụng Agent Skill cho feature cụ thể: [https://youtu.be/JEgvsOjCcZc]https://youtu.be/JEgvsOjCcZc

---
