# Bug Report — FR14: Category Management

## 1. Bug Reports

### BUG-01: Thêm được danh mục với tên rỗng

| Field                  | Detail                                                                                                |
| ---------------------- | ----------------------------------------------------------------------------------------------------- |
| **Bug ID**             | BUG-01                                                                                                |
| **Phát hiện tại**      | TC-06 (Domain Testing) + BVA-01 (BVA) — cùng một lỗi                                                  |
| **Severity**           | Major                                                                                                 |
| **Priority**           | High                                                                                                  |
| **Summary**            | Hệ thống cho phép thêm danh mục khi để tên trống (chuỗi rỗng `""`), không có validation bắt buộc nhập |
| **Steps to Reproduce** | 1. Đăng nhập admin <br>2. Vào trang quản lý danh mục <br>3. Để trống tên danh mục <br>4. Nhấn thêm    |
| **Expected**           | Báo lỗi "Tên danh mục là bắt buộc", không cho thêm                                                    |
| **Actual**             | Thêm thành công, danh mục mới xuất hiện với tên rỗng                                                  |
| **Screenshot**         | `screenshots/bug-01-empty-category.png`                                                               |

### BUG-02: Tên chỉ chứa khoảng trắng vẫn thêm được (không validate sau trim)

| Field                  | Detail                                                                                                                                                                               |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Bug ID**             | BUG-02                                                                                                                                                                               |
| **Phát hiện tại**      | TC-07 (Domain Testing) + BVA-07, BVA-08 (BVA) — cùng một lỗi                                                                                                                         |
| **Severity**           | Minor                                                                                                                                                                                |
| **Priority**           | Medium                                                                                                                                                                               |
| **Summary**            | Khi tên danh mục chỉ chứa một hoặc nhiều khoảng trắng, hệ thống có trim chuỗi nhưng không báo lỗi chuỗi rỗng sau khi trim, dẫn đến thêm thành công danh mục với tên rỗng về bản chất |
| **Steps to Reproduce** | 1. Đăng nhập admin <br>2. Vào trang quản lý danh mục <br>3. Nhập tên chỉ gồm khoảng trắng (ví dụ: `"   "`) <br>4. Nhấn thêm                                                          |
| **Expected**           | Sau khi trim, hệ thống nhận ra chuỗi rỗng và báo lỗi "Tên danh mục là bắt buộc"                                                                                                      |
| **Actual**             | Có trim text nhưng không báo lỗi, thêm thành công với tên rỗng                                                                                                                       |

### BUG-03: Không giới hạn độ dài tên danh mục

| Field                  | Detail                                                                                                                                                                    |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bug ID**             | BUG-03                                                                                                                                                                    |
| **Phát hiện tại**      | TC-08 (Domain Testing) + BVA-04, BVA-05, BVA-06 (BVA) — cùng một lỗi                                                                                                      |
| **Severity**           | Minor                                                                                                                                                                     |
| **Priority**           | Medium                                                                                                                                                                    |
| **Summary**            | Hệ thống không giới hạn độ dài tên danh mục — chấp nhận tên dài 255, 256, thậm chí 1000 ký tự mà không báo lỗi hay cắt bớt, có nguy cơ vượt giới hạn cột VARCHAR trong DB |
| **Steps to Reproduce** | 1. Đăng nhập admin <br>2. Vào trang quản lý danh mục <br>3. Nhập tên danh mục dài 255+ ký tự (thử thêm ở mức 256 và 1000 ký tự) <br>4. Nhấn thêm                          |
| **Expected**           | Báo lỗi hoặc cắt bớt khi vượt quá giới hạn cho phép (ví dụ VARCHAR(255))                                                                                                  |
| **Actual**             | Thêm thành công ở mọi độ dài đã thử, không có giới hạn nào được áp dụng                                                                                                   |
| **Screenshot**         | `screenshots/bug-03-no-length-limit.png`                                                                                                                                  |

### BUG-04: Cho phép thêm danh mục trùng tên đã tồn tại

| Field                  | Detail                                                                                                                                                                         |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Bug ID**             | BUG-04                                                                                                                                                                         |
| **Phát hiện tại**      | TC-09 (Domain Testing)                                                                                                                                                         |
| **Severity**           | Major                                                                                                                                                                          |
| **Priority**           | High                                                                                                                                                                           |
| **Summary**            | Hệ thống không kiểm tra trùng lặp tên danh mục — cho phép thêm danh mục có tên giống hệt danh mục đã tồn tại, dẫn đến dữ liệu trùng lặp trong danh sách                        |
| **Steps to Reproduce** | 1. Đăng nhập admin <br>2. Thêm một danh mục với tên bất kỳ (ví dụ: "Điện thoại") <br>3. Thêm tiếp một danh mục khác với tên trùng hoàn toàn <br>4. Kiểm tra danh sách danh mục |
| **Expected**           | Báo lỗi "Tên danh mục đã tồn tại", không cho thêm trùng                                                                                                                        |
| **Actual**             | Vẫn thêm thành công, danh sách hiển thị 2 danh mục trùng tên                                                                                                                   |
| **Screenshot**         | `screenshots/bug-04-duplicate-category.png`                                                                                                                                    |

### BUG-05: Xóa danh mục đang có sản phẩm mà không cảnh báo

| Field                  | Detail                                                                                                                                                                     |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bug ID**             | BUG-05                                                                                                                                                                     |
| **Phát hiện tại**      | TC-13 (Domain Testing)                                                                                                                                                     |
| **Severity**           | Major                                                                                                                                                                      |
| **Priority**           | High                                                                                                                                                                       |
| **Summary**            | Khi xóa một danh mục đang chứa sản phẩm, hệ thống không hiển thị cảnh báo và vẫn xóa thành công, có nguy cơ để lại sản phẩm mồ côi (orphaned) không còn thuộc danh mục nào |
| **Steps to Reproduce** | 1. Đăng nhập admin <br>2. Chọn một danh mục đang có sản phẩm liên kết <br>3. Nhấn xóa danh mục đó <br>4. Kiểm tra kết quả                                                  |
| **Expected**           | Hiển thị thông báo cảnh báo "Danh mục đang có sản phẩm tương ứng" trước khi cho phép xóa (hoặc chặn xóa)                                                                   |
| **Actual**             | Xóa thành công ngay lập tức, không có thông báo hay cảnh báo nào                                                                                                           |

### BUG-06: API tạo danh mục không kiểm tra role — user thường vẫn tạo được danh mục

| Field                  | Detail                                                                                                                                                      |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bug ID**             | BUG-06                                                                                                                                                      |
| **Phát hiện tại**      | TC-16 (Domain Testing)                                                                                                                                      |
| **Severity**           | Critical                                                                                                                                                    |
| **Priority**           | High                                                                                                                                                        |
| **Related**            | SEC-03                                                                                                                                                      |
| **Summary**            | User thường dùng JWT token của mình gọi được API `POST /api/categories`, tạo danh mục thành công dù không có quyền admin — vi phạm SEC-03                   |
| **Steps to Reproduce** | 1. Đăng nhập tài khoản user thường → lấy JWT token <br>2. Gọi `POST /api/categories` với token đó và tên danh mục hợp lệ <br>3. Kiểm tra danh sách danh mục |
| **Expected**           | Trả về 403 Forbidden vì token không có role=admin                                                                                                           |
| **Actual**             | Trả về 200, danh mục mới được tạo thành công và xuất hiện trong danh sách                                                                                   |
| **Screenshot**         | `screenshots/bug-06-sec03-role-bypass.png`                                                                                                                  |

---

## 2. AI Gap Analysis (gộp)

| Gap                              | Mô tả                                   | Lý do AI bỏ sót                                                                                                                       |
| -------------------------------- | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| BUG-01 (empty name)              | Thêm được danh mục rỗng                 | AI giả định validation được implement đúng theo spec, không kiểm tra thực tế                                                          |
| BUG-02 (whitespace)              | Tên chỉ có khoảng trắng vẫn thêm được   | AI không tự nghĩ đến edge case trim() nếu không được nhắc rõ                                                                          |
| BUG-03 (max length)              | Không giới hạn độ dài tên               | AI không biết schema DB thực tế (kiểu cột, độ dài VARCHAR), chỉ suy đoán dựa trên spec                                                |
| BUG-04 (duplicate name)          | Trùng tên vẫn thêm được                 | AI không biết ràng buộc unique có được áp dụng ở DB/backend hay không                                                                 |
| BUG-05 (delete with products)    | Xóa danh mục có sản phẩm không cảnh báo | AI không biết quan hệ dữ liệu thực tế giữa bảng categories và products trong DB                                                       |
| BUG-06 (role bypass)             | User thường tạo được danh mục qua API   | Cần test thực tế bằng cách gọi API trực tiếp với token khác quyền — AI không tự thực hiện nếu không được yêu cầu security test cụ thể |
| TC-10, TC-17 (XSS/SQL injection) | Đã test và Pass — hệ thống sanitize tốt | Không phải gap, nhưng cần lưu ý AI thường bỏ qua các test case bảo mật này nếu không được nhắc chủ động                               |

---

## 3. Test Summary (gộp)

| Metric            | Value                   |
| ----------------- | ----------------------- |
| Total test cases  | 27 (17 Domain + 10 BVA) |
| Executed          | 27                      |
| Passed            | 15                      |
| Failed            | 12                      |
| Not executed      | 0                       |
| Unique bugs found | 6                       |
