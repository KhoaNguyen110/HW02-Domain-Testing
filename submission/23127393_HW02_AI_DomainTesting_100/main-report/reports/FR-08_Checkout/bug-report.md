# Bug Reports — FR08 Checkout

## 1. Bug Reports

### BUG-01: Tổng tiền (total_amount) có thể chỉnh sửa trực tiếp trên UI

| Field                  | Detail                                                                                                              |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------- |
| **Bug ID**             | BUG-01                                                                                                              |
| **Phát hiện tại**      | TC-06 (Domain Testing) + BVA-07, BVA-08 (BVA) — cùng một lỗi                                                        |
| **Severity**           | Critical                                                                                                            |
| **Priority**           | High                                                                                                                |
| **Summary**            | Field tổng tiền trên trang checkout không phải readonly — người dùng chỉnh sửa trực tiếp trên UI được, vi phạm spec |
| **Steps to Reproduce** | 1. Vào trang checkout <br>2. Thêm sản phẩm vào giỏ <br>3. Thử click/sửa trực tiếp vào ô hiển thị tổng tiền          |
| **Expected**           | Tổng tiền là readonly, không cho chỉnh sửa trên UI                                                                  |
| **Actual**             | Người dùng có thể sửa giá trị tổng tiền trực tiếp trên UI                                                           |
| **Screenshot**         | `screenshots/bug-01-editable-total.png`                                                                             |

### BUG-02: Giỏ hàng không bị xóa sau khi thanh toán thành công

| Field                  | Detail                                                                                                               |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------- |
| **Bug ID**             | BUG-02                                                                                                               |
| **Phát hiện tại**      | TC-01, TC-12 (Domain Testing)                                                                                        |
| **Severity**           | Major                                                                                                                |
| **Priority**           | High                                                                                                                 |
| **Summary**            | Sau khi thanh toán thành công, giỏ hàng vẫn còn nguyên sản phẩm thay vì được xóa                                     |
| **Steps to Reproduce** | 1. Đăng nhập, thêm sản phẩm vào giỏ <br>2. Điền đầy đủ thông tin và checkout thành công <br>3. Kiểm tra lại giỏ hàng |
| **Expected**           | Giỏ hàng rỗng sau khi thanh toán thành công                                                                          |
| **Actual**             | Giỏ hàng vẫn còn đầy đủ sản phẩm                                                                                     |
| **Screenshot**         | `screenshots/bug-02-cart-not-cleared.png`                                                                            |

### BUG-03: Có thể checkout thành công khi thiếu địa chỉ và/hoặc SĐT

| Field                  | Detail                                                                                                    |
| ---------------------- | --------------------------------------------------------------------------------------------------------- |
| **Bug ID**             | BUG-03                                                                                                    |
| **Phát hiện tại**      | TC-09, TC-10, TC-11 (Domain Testing)                                                                      |
| **Severity**           | Major                                                                                                     |
| **Priority**           | High                                                                                                      |
| **Summary**            | Hệ thống cho phép thanh toán thành công dù người dùng chưa điền địa chỉ và/hoặc SĐT ở trang profile       |
| **Steps to Reproduce** | 1. Đăng nhập với profile thiếu địa chỉ và/hoặc SĐT <br>2. Thêm sản phẩm vào giỏ <br>3. Tiến hành checkout |
| **Expected**           | Báo lỗi yêu cầu điền đủ thông tin giao hàng trước khi checkout                                            |
| **Actual**             | Checkout thành công, đơn hàng được tạo dù thiếu thông tin giao hàng                                       |
| **Screenshot**         | `screenshots/bug-03-missing-profile-checkout.png`                                                         |

### BUG-04: Backend chấp nhận total_amount do client gửi lên, không tính lại

| Field                  | Detail                                                                                                                                                                                             |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bug ID**             | BUG-04                                                                                                                                                                                             |
| **Phát hiện tại**      | TC-06, TC-07, TC-08 (Domain Testing — ghi nhận "cần verify") + BVA-04, BVA-05, BVA-06, BVA-07, BVA-08 (BVA — đã xác nhận qua DevTools)                                                             |
| **Severity**           | Critical                                                                                                                                                                                           |
| **Priority**           | High                                                                                                                                                                                               |
| **Summary**            | Backend hoàn toàn không tính lại tổng tiền phía server — chấp nhận bất kỳ giá trị `total_amount` nào client gửi lên, bao gồm 0, số âm, giá trị thấp hơn hoặc cao hơn giá trị thực tế của giỏ hàng  |
| **Steps to Reproduce** | 1. Mở DevTools → Network <br>2. Thêm sản phẩm vào giỏ <br>3. Intercept request checkout <br>4. Sửa `total_amount` thành 0, số âm, hoặc giá trị bất kỳ khác giá trị thực <br>5. Gửi request         |
| **Expected**           | Backend luôn tự tính lại tổng tiền dựa trên giỏ hàng thực tế phía server, bỏ qua hoàn toàn giá trị `total_amount` client gửi                                                                       |
| **Actual**             | Backend chấp nhận toàn bộ giá trị do client gửi lên trong mọi trường hợp (0, âm, thấp hơn, cao hơn) — đây là lỗ hổng bảo mật nghiêm trọng, có thể lợi dụng để thanh toán với giá 0đ hoặc giá tùy ý |
| **Screenshot**         | `screenshots/bug-04-total-manipulation.png`                                                                                                                                                        |

---

## 2. AI Gap Analysis

| Gap                                  | Mô tả                                      | Lý do AI bỏ sót                                                                                                                            |
| ------------------------------------ | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------ |
| BUG-01 (editable field)              | UI cho sửa tổng tiền trực tiếp             | AI không render/chạy UI thực tế, không phân biệt được input field và readonly field                                                        |
| BUG-02 (cart not cleared)            | Giỏ hàng không xóa sau checkout            | AI giả định spec được implement đúng, không kiểm tra state thực tế sau hành động                                                           |
| BUG-03 (missing profile)             | Checkout được dù thiếu địa chỉ/SĐT         | AI không biết thông tin profile nằm ở trang khác, không liên kết được ràng buộc giữa 2 module                                              |
| BUG-04 (total manipulation)          | Client gửi total_amount giả (0, âm, tùy ý) | Cần test thực tế qua DevTools/intercept network request — AI không thể thực hiện thao tác này, chỉ có thể nghi ngờ và đề xuất "cần verify" |
| BVA-11 (quantity=999, chưa thực thi) | Overflow hoặc performance khi số lượng lớn | AI không tự nghĩ ra stress test nếu không được nhắc, và cần môi trường thật để đo hiệu năng                                                |

---

## 3. Test Summary

| Metric            | Value                                                                |
| ----------------- | -------------------------------------------------------------------- |
| Total test cases  | 25 (14 Domain + 11 BVA)                                              |
| Executed          | 24                                                                   |
| Passed            | 11                                                                   |
| Failed            | 13                                                                   |
| Not executed      | 1 (BVA-11 — stress test quantity=999)                                |
| Unique bugs found | 4 (sau khi loại trùng: BUG-01 và BUG-04 là kết quả gộp từ cả 2 file) |
