# FR-14: Quản lý Danh mục (Category CRUD) — Domain Testing Report

## 1. Feature Overview

| Item         | Detail                                   |
| ------------ | ---------------------------------------- |
| Feature ID   | FR-14                                    |
| Feature Name | Quản lý Danh mục (Category CRUD)         |
| URL          | `http://localhost:5173/admin/categories` |
| Technique    | Domain Testing                           |

### Mô tả

Admin có thể Thêm / Xem / Xóa danh mục. Tên danh mục là bắt buộc, không được để trống.

---

## 2. Domain Testing — Step-by-step

### Bước 1: Xác định biến input

| Biến            | Kiểu    | Mô tả                                                |
| --------------- | ------- | ---------------------------------------------------- |
| `auth_role`     | Enum    | Quyền truy cập: admin / user thường / chưa đăng nhập |
| `category_name` | String  | Tên danh mục khi thêm mới                            |
| `category_id`   | Integer | ID danh mục khi xóa                                  |

---

### Bước 2: Phân vùng miền (Domain Partitioning)

#### Biến `auth_role`

| Miền | Loại    | Điều kiện                          | Kết quả mong đợi                     |
| ---- | ------- | ---------------------------------- | ------------------------------------ |
| R1   | Valid   | Đã đăng nhập với quyền Admin       | Truy cập được trang quản lý danh mục |
| R2   | Invalid | Đã đăng nhập với quyền User thường | Bị từ chối truy cập (403)            |
| R3   | Invalid | Chưa đăng nhập                     | Redirect về trang login              |

#### Biến `category_name` (khi thêm mới)

| Miền | Loại    | Điều kiện                    | Ví dụ                         |
| ---- | ------- | ---------------------------- | ----------------------------- |
| N1   | Valid   | Tên hợp lệ, không rỗng       | `"Điện thoại"`                |
| N2   | Valid   | Tên có khoảng trắng ở giữa   | `"Đồ gia dụng"`               |
| N3   | Invalid | Rỗng hoàn toàn               | `""`                          |
| N4   | Invalid | Chỉ có khoảng trắng          | `"   "`                       |
| N5   | Edge    | Tên rất dài (stress test)    | 255+ ký tự                    |
| N6   | Edge    | Tên trùng với danh mục đã có | `"Điện thoại"` (đã tồn tại)   |
| N7   | Edge    | Tên có ký tự đặc biệt        | `"<script>alert(1)</script>"` |
| N8   | Edge    | Tên chỉ có số                | `"12345"`                     |

#### Biến `category_id` (khi xóa)

| Miền | Loại    | Điều kiện                 | Kết quả mong đợi                   |
| ---- | ------- | ------------------------- | ---------------------------------- |
| I1   | Valid   | ID tồn tại trong DB       | Xóa thành công                     |
| I2   | Invalid | ID không tồn tại          | Báo lỗi không tìm thấy             |
| I3   | Edge    | Danh mục đang có sản phẩm | Cần xác định: xóa được hay báo lỗi |

---

### Bước 3: Chọn test case đại diện

---

## 3. Test Case Table

| TC ID | Mô tả                                        | auth_role        | category_name                 | category_id    | Kết quả mong đợi                                                     | Kết quả thực tế                                          | Pass/Fail |
| ----- | -------------------------------------------- | ---------------- | ----------------------------- | -------------- | -------------------------------------------------------------------- | -------------------------------------------------------- | --------- |
| TC-01 | Admin xem danh sách danh mục                 | R1 admin         | -                             | -              | Hiển thị đầy đủ danh sách danh mục                                   | Hiển thị đầy đủ danh sách danh mục                       | Pass      |
| TC-02 | User thường truy cập trang admin             | R2 user          | -                             | -              | Bị từ chối, báo 403 hoặc redirect                                    | Bị từ chối quyền truy cập                                | Pass      |
| TC-03 | Chưa đăng nhập truy cập trang admin          | R3 not logged in | -                             | -              | Redirect về `/login`                                                 | Redirect về `/login`                                     | Pass      |
| TC-04 | Thêm danh mục hợp lệ                         | R1 admin         | N1 `"Điện thoại"`             | -              | Thêm thành công, hiển thị trong danh sách                            | Thêm thành công, hiển thị trong danh sách                | Pass      |
| TC-05 | Thêm danh mục tên có khoảng trắng giữa       | R1 admin         | N2 `"Đồ gia dụng"`            | -              | Thêm thành công                                                      | Thêm thành công, hiển thị trong danh sách                | Pass      |
| TC-06 | Thêm danh mục tên rỗng                       | R1 admin         | N3 `""`                       | -              | Báo lỗi: tên danh mục là bắt buộc                                    | Thêm thành công với chuỗi rỗng                           | Fail      |
| TC-07 | Thêm danh mục chỉ có khoảng trắng            | R1 admin         | N4 `"   "`                    | -              | Báo lỗi: tên danh mục là bắt buộc                                    | Thêm thành công với chuỗi rỗng                           | Fail      |
| TC-08 | Thêm danh mục tên rất dài                    | R1 admin         | N5 255+ ký tự                 | -              | Báo lỗi hoặc cắt bớt                                                 | Thêm thành công, hiển thị trong danh sách                | Fail      |
| TC-09 | Thêm danh mục trùng tên đã có                | R1 admin         | N6 tên trùng                  | -              | Báo lỗi tên đã tồn tại                                               | Vẫn thêm thành công, hiển thị trùng lặp trong danh sách  | Fail      |
| TC-10 | Thêm danh mục tên có script injection        | R1 admin         | N7 `<script>`                 | -              | Sanitize hoặc báo lỗi, không execute script                          | Thêm thành công với tên hiển thị dạng text               | Pass      |
| TC-11 | Thêm danh mục tên chỉ có số                  | R1 admin         | N8 `"12345"`                  | -              | Thêm thành công (số là tên hợp lệ)                                   | Thêm thành công, hiển thị trong danh sách                |           |
| TC-12 | Xóa danh mục tồn tại                         | R1 admin         | -                             | I1 valid ID    | Xóa thành công, biến mất khỏi danh sách                              | Xóa thành công, biến mất khỏi danh sách                  | Pass      |
| TC-13 | Xóa danh mục đang có sản phẩm                | R1 admin         | -                             | I3 có sản phẩm | Thông báo danh mục đang có sản phẩm tương ứng                        | Vẫn xóa được, không có thông báo                         | Fail      |
| TC-14 | Xem danh sách sau khi thêm                   | R1 admin         | N1 mới thêm                   | -              | Danh mục mới xuất hiện ngay trong danh sách                          | Danh mục mới xuất hiện ngay trong danh sách              | Pass      |
| TC-15 | Xem danh sách sau khi xóa                    | R1 admin         | -                             | I1 vừa xóa     | Danh mục đã xóa biến mất khỏi danh sách                              | Danh mục đã xóa biến mất khỏi danh sách                  | Pass      |
| TC-16 | Dùng token user thường gọi API thêm danh mục | R2 user token    | N1 valid name                 | -              | Bị từ chối 403, không thêm được                                      | Dùng user token vẫn thêm được category mới vào danh sách | Fail      |
| TC-17 | Tên danh mục có SQL injection                | R1 admin         | `'; DROP TABLE categories;--` | -              | Sanitize, không execute SQL, thêm như chuỗi bình thường hoặc báo lỗi | Không execute SQL, thêm tên dạng text bình thường        | Pass      |

---

## 4. Bug Reports

### BUG-01: Có thể thêm danh mục với tên rỗng

| Field             | Detail                                                                                                |
| ----------------- | ----------------------------------------------------------------------------------------------------- |
| **Bug ID**        | BUG-01                                                                                                |
| **Phát hiện tại** | TC-06                                                                                                 |
| **Severity**      | Major                                                                                                 |
| **Priority**      | High                                                                                                  |
| **Summary**       | Hệ thống cho phép thêm danh mục khi để tên trống, danh mục mới có tên là chuỗi rỗng `""`              |
| **Expected**      | Báo lỗi "Tên danh mục là bắt buộc", không cho thêm                                                    |
| **Actual**        | Thêm thành công, danh mục mới xuất hiện với tên rỗng                                                  |
| **Steps**         | 1. Đăng nhập admin <br> 2. Vào trang quản lý danh mục <br> 3. Để trống tên danh mục <br> 4. Nhấn thêm |
| **Screenshot**    | `screenshots/bug-01-empty-category.png`                                                               |

### BUG-02: API admin không kiểm tra role — user thường tạo được danh mục

| Field             | Detail                                                                                                                                            |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bug ID**        | BUG-03                                                                                                                                            |
| **Phát hiện tại** | TC-17                                                                                                                                             |
| **Severity**      | Critical                                                                                                                                          |
| **Priority**      | High                                                                                                                                              |
| **Summary**       | User thường dùng JWT token của mình gọi được API `POST /api/categories`, tạo danh mục thành công — vi phạm SEC-03                                 |
| **Expected**      | Trả về 403 Forbidden vì token không có role=admin                                                                                                 |
| **Actual**        | Trả về 200, danh mục được tạo thành công                                                                                                          |
| **Steps**         | 1. Đăng nhập tài khoản user thường → lấy JWT token <br> 2. Gọi `POST /api/categories` với token đó <br> 3. Danh mục mới xuất hiện trong danh sách |
| **Related**       | SEC-03                                                                                                                                            |
| **Screenshot**    | `screenshots/bug-02-sec03-role-bypass.png`                                                                                                        |

---

## 5. AI Gap Analysis

| Gap                          | Mô tả                                        | Lý do AI bỏ sót                                     |
| ---------------------------- | -------------------------------------------- | --------------------------------------------------- |
| BUG-01 (empty name)          | Thêm được danh mục rỗng                      | AI assume validation được implement đúng theo spec  |
| TC-07 (whitespace)           | Tên chỉ có khoảng trắng vẫn có thể thêm được | AI không test edge case trim()                      |
| TC-10 (XSS injection)        | Tên có script tag                            | AI không nghĩ đến security test nếu không được nhắc |
| TC-14 (delete with products) | Xóa danh mục có sản phẩm                     | AI không biết quan hệ dữ liệu thực tế trong DB      |

---

## 6. Test Summary

| Metric           | Value |
| ---------------- | ----- |
| Total test cases | 17    |
| Executed         | 17    |
| Passed           | 9     |
| Failed           | 8     |
| Not executed     | 0     |
| Bugs found       | 2     |
