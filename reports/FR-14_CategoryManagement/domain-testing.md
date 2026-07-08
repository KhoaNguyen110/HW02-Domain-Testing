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
