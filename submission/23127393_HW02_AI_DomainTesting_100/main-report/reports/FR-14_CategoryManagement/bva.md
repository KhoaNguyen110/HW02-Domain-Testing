# FR-14: Quản lý Danh mục (Category CRUD) — Boundary Value Analysis Report

## 1. Feature Overview

| Item         | Detail                                   |
| ------------ | ---------------------------------------- |
| Feature ID   | FR-14                                    |
| Feature Name | Quản lý Danh mục (Category CRUD)         |
| URL          | `http://localhost:5173/admin/categories` |
| Technique    | Boundary Value Analysis (BVA)            |

---

## 2. Xác định các biên (Boundaries)

| Biến            | Ràng buộc                        | Nguồn      |
| --------------- | -------------------------------- | ---------- |
| `category_name` | Không được rỗng (length > 0)     | Spec       |
| `category_name` | Độ dài tối đa chưa rõ trong spec | Cần verify |

---

## 3. Phân tích biên từng biến

### 3.1 Biến `category_name` — Độ dài (length)

Spec chỉ nêu "không được để trống" → biên dưới là **0 ký tự**.

| Điểm biên                | Độ dài           | Tên                 | Kết quả mong đợi      |
| ------------------------ | ---------------- | ------------------- | --------------------- |
| **On-point (biên dưới)** | **0 ký tự** `""` | Rỗng hoàn toàn      | Báo lỗi bắt buộc nhập |
| Just above boundary      | 1 ký tự `"a"`    | Tối thiểu hợp lệ    | Thêm thành công       |
| In-range                 | 2 ký tự `"ab"`   | Bình thường         | Thêm thành công       |
| In-range                 | 50 ký tự         | Độ dài thông thường | Thêm thành công       |

### 3.2 Biến `category_name` — Biên trên (max length)

Spec không nêu max length → cần tìm biên trên bằng cách test tăng dần.

| Điểm biên      | Độ dài     | Kết quả mong đợi                      |
| -------------- | ---------- | ------------------------------------- |
| In-range       | 100 ký tự  | Thêm thành công                       |
| Test boundary  | 255 ký tự  | Thêm thành công hoặc báo lỗi (tùy DB) |
| Above boundary | 256 ký tự  | Báo lỗi nếu DB giới hạn VARCHAR(255)  |
| Far above      | 1000 ký tự | Báo lỗi                               |

### 3.3 Biến `category_name` — Khoảng trắng (whitespace)

Đây là biên đặc biệt: chuỗi không rỗng về length nhưng rỗng về nội dung sau trim.

| Điểm biên               | Giá trị                | Kết quả mong đợi                 |
| ----------------------- | ---------------------- | -------------------------------- |
| **On-point whitespace** | `"   "` (chỉ space)    | Báo lỗi sau khi trim             |
| Just above              | `" a"` (space + ký tự) | Thêm thành công (trim space đầu) |
| Valid                   | `"Máy tính bảng"`      | Thêm thành công                  |

---

## 4. Test Case Table

| TC ID  | Mô tả                            | Input                | Biên liên quan                 | Kết quả mong đợi                  | Kết quả thực tế                                                     | Pass/Fail |
| ------ | -------------------------------- | -------------------- | ------------------------------ | --------------------------------- | ------------------------------------------------------------------- | --------- |
| BVA-01 | Tên rỗng hoàn toàn               | `""` (0 ký tự)       | **On-point length=0**          | Báo lỗi bắt buộc nhập tên         | Không thông báo lỗi, thêm thành công danh mục mới với nội dung rỗng | Fail      |
| BVA-02 | Tên 1 ký tự — tối thiểu hợp lệ   | `"a"` (1 ký tự)      | Just above length=1            | Thêm thành công                   | Thêm thành công                                                     | Pass      |
| BVA-03 | Tên 2 ký tự                      | `"ab"` (2 ký tự)     | In-range length=2              | Thêm thành công                   | Thêm thành công                                                     | Pass      |
| BVA-04 | Tên 255 ký tự                    | 255 ký tự `"aaa..."` | Test upper boundary            | Thêm thành công hoặc báo lỗi      | Thêm thành công                                                     | Pass      |
| BVA-05 | Tên 256 ký tự                    | 256 ký tự `"aaa..."` | Just above upper boundary      | Báo lỗi nếu giới hạn VARCHAR(255) | Thêm thành công                                                     | Pass      |
| BVA-06 | Tên 1000 ký tự                   | 1000 ký tự           | Far above upper boundary       | Báo lỗi                           | Vẫn thêm thành công                                                 | Fail      |
| BVA-07 | Tên chỉ có 1 khoảng trắng        | `" "` (1 space)      | On-point whitespace            | Báo lỗi sau khi trim              | Có trim text nhưng không báo lỗi chuỗi rỗng                         | Fail      |
| BVA-08 | Tên chỉ có nhiều khoảng trắng    | `"   "` (3 spaces)   | On-point whitespace            | Báo lỗi sau khi trim              | Có trim text nhưng không báo lỗi chuỗi rỗng                         | Fail      |
| BVA-09 | Tên có khoảng trắng đầu + ký tự  | `" a"`               | Just above whitespace boundary | Thêm thành công, trim space đầu   | Có trim text và thêm thành công                                     | Pass      |
| BVA-10 | Tên có khoảng trắng cuối + ký tự | `"a "`               | Just above whitespace boundary | Thêm thành công, trim space cuối  | Có trim text và thêm thành công                                     | Pass      |
