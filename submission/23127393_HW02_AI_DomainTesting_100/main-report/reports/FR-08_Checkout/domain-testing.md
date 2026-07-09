# FR-08: Thanh toán (Checkout) — Domain Testing Report

## 1. Feature Overview

| Item         | Detail                           |
| ------------ | -------------------------------- |
| Feature ID   | FR-08                            |
| Feature Name | Thanh toán (Checkout)            |
| URL          | `http://localhost:5173/checkout` |
| Technique    | Domain Testing                   |

### Mô tả

Người dùng đã đăng nhập có thể tiến hành thanh toán từ giỏ hàng. Tổng tiền được tính tự động từ giỏ hàng, backend tự tính lại và không chấp nhận giá trị `total_amount` do client gửi lên. Sau thanh toán thành công, giỏ hàng được xóa.

---

## 2. Domain Testing — Step-by-step

### Bước 1: Xác định biến input

| Biến           | Kiểu    | Mô tả                             |
| -------------- | ------- | --------------------------------- |
| `auth_status`  | Boolean | Người dùng đã đăng nhập hay chưa  |
| `cart_items`   | List    | Danh sách sản phẩm trong giỏ hàng |
| `total_amount` | Float   | Tổng tiền thanh toán              |
| `user_profile` | Object  | Thông tin cá nhân (địa chỉ, SĐT)  |

---

### Bước 2: Phân vùng miền (Domain Partitioning)

#### Biến `auth_status`

| Miền | Loại    | Điều kiện      | Kết quả mong đợi            |
| ---- | ------- | -------------- | --------------------------- |
| A1   | Valid   | Đã đăng nhập   | Cho phép vào trang checkout |
| A2   | Invalid | Chưa đăng nhập | Redirect về trang login     |

#### Biến `cart_items`

| Miền | Loại    | Điều kiện                      | Kết quả mong đợi                            |
| ---- | ------- | ------------------------------ | ------------------------------------------- |
| C1   | Valid   | Giỏ hàng có ít nhất 1 sản phẩm | Hiển thị danh sách, cho phép checkout       |
| C2   | Invalid | Giỏ hàng rỗng                  | Không cho phép checkout, báo giỏ hàng trống |
| C3   | Edge    | Giỏ hàng có 1 sản phẩm         | Hiển thị đúng, cho phép checkout            |
| C4   | Edge    | Giỏ hàng có nhiều sản phẩm     | Hiển thị đầy đủ, tính tổng đúng             |

#### Biến `total_amount`

| Miền | Loại    | Điều kiện                     | Kết quả mong đợi                     |
| ---- | ------- | ----------------------------- | ------------------------------------ |
| T1   | Valid   | Tổng tiền do backend tính     | Đúng với giỏ hàng                    |
| T2   | Invalid | Client tự sửa `total_amount`  | Backend bỏ qua, tính lại từ giỏ hàng |
| T3   | Invalid | Client gửi `total_amount = 0` | Backend bỏ qua, tính lại đúng        |
| T4   | Invalid | Client gửi `total_amount` âm  | Backend bỏ qua, tính lại đúng        |

#### Biến `user_profile`

| Miền | Loại    | Điều kiện                | Kết quả mong đợi                           |
| ---- | ------- | ------------------------ | ------------------------------------------ |
| U1   | Valid   | Có đầy đủ địa chỉ, SĐT   | Cho phép checkout                          |
| U2   | Invalid | Thiếu địa chỉ            | Cần xác định: báo lỗi hay vẫn cho checkout |
| U3   | Invalid | Thiếu SĐT                | Cần xác định: báo lỗi hay vẫn cho checkout |
| U4   | Invalid | Thiếu cả địa chỉ lẫn SĐT | Cần xác định: báo lỗi hay vẫn cho checkout |

---

### Bước 3: Chọn test case đại diện

---

## 3. Test Case Table

| TC ID | Mô tả                                    | auth             | cart              | total_amount    | profile          | Kết quả mong đợi                                      | Kết quả thực tế                                                           | Pass/Fail |
| ----- | ---------------------------------------- | ---------------- | ----------------- | --------------- | ---------------- | ----------------------------------------------------- | ------------------------------------------------------------------------- | --------- |
| TC-01 | Checkout thành công đầy đủ thông tin     | A1 logged in     | C1 có hàng        | T1 backend tính | U1 đầy đủ        | Thanh toán thành công, giỏ hàng xóa, lịch sử cập nhật | Giỏ hàng không được xóa sau khi thanh toán thành công                     | Fail      |
| TC-02 | Chưa đăng nhập vào trang checkout        | A2 not logged in | C1 có hàng        | -               | -                | Redirect về `/login`                                  | Hiển thị thông báo "Cần đăng nhập để thanh toán", redirect về trang login | Pass      |
| TC-03 | Giỏ hàng rỗng                            | A1 logged in     | C2 rỗng           | -               | U1 đầy đủ        | Báo giỏ hàng trống, không cho checkout                | Thông báo "Giỏ hàng trống", không cho checkout                            | Pass      |
| TC-04 | Giỏ hàng 1 sản phẩm                      | A1 logged in     | C3 1 sản phẩm     | T1 backend tính | U1 đầy đủ        | Hiển thị đúng 1 sản phẩm, tổng tiền đúng              | Hiển thị đúng số tiền thanh toán                                          | Pass      |
| TC-05 | Giỏ hàng nhiều sản phẩm                  | A1 logged in     | C4 nhiều sản phẩm | T1 backend tính | U1 đầy đủ        | Hiển thị đầy đủ, tổng tiền đúng                       | Hiển thị đúng số tiền thanh toán                                          | Pass      |
| TC-06 | Client tự sửa total_amount trên UI       | A1 logged in     | C1 có hàng        | T2 client sửa   | U1 đầy đủ        | Backend tính lại đúng, không dùng giá trị client gửi  | Không tính lại giá trị đúng, chấp nhận thông tin sửa đổi từ client        | Fail      |
| TC-07 | Client gửi total_amount = 0              | A1 logged in     | C1 có hàng        | T3 = 0          | U1 đầy đủ        | Backend tính lại đúng, không chấp nhận 0              | Chấp nhận total_amount = 0                                                | Fail      |
| TC-08 | Client gửi total_amount âm               | A1 logged in     | C1 có hàng        | T4 âm           | U1 đầy đủ        | Backend tính lại đúng, không chấp nhận giá trị âm     | Chấp nhận giá trị total_amount âm                                         | Fail      |
| TC-09 | Thiếu địa chỉ vẫn checkout               | A1 logged in     | C1 có hàng        | T1 backend tính | U2 thiếu địa chỉ | Báo lỗi thiếu địa chỉ                                 | Vẫn checkout thành công khi chưa có địa chỉ nhận hàng                     | Fail      |
| TC-10 | Thiếu SĐT vẫn checkout                   | A1 logged in     | C1 có hàng        | T1 backend tính | U3 thiếu SĐT     | Báo lỗi thiếu SĐT                                     | Vẫn checkout thành công khi chưa có số điện thoại người nhận hàng         | Fail      |
| TC-11 | Thiếu cả địa chỉ lẫn SĐT                 | A1 logged in     | C1 có hàng        | T1 backend tính | U4 thiếu hết     | Báo lỗi thiếu thông tin                               | Vẫn checkout thành công                                                   | Fail      |
| TC-12 | Sau checkout thành công giỏ hàng xóa     | A1 logged in     | C1 có hàng        | T1 backend tính | U1 đầy đủ        | Giỏ hàng rỗng sau khi thanh toán                      | Không xóa giỏ hàng sau khi thanh toán thành công                          | Fail      |
| TC-13 | Sau checkout thành công lịch sử cập nhật | A1 logged in     | C1 có hàng        | T1 backend tính | U1 đầy đủ        | Đơn hàng xuất hiện trong lịch sử                      | Lịch sử được cập nhật chính xác                                           | Pass      |
| TC-14 | Tổng tiền hiển thị đúng với giỏ hàng     | A1 logged in     | C4 nhiều sản phẩm | T1 backend tính | U1 đầy đủ        | Tổng tiền = sum(giá × số lượng từng sản phẩm)         | Tổng tiền được tính chính xác với danh sách sản phẩm trong giỏ hàng       | Pass      |
