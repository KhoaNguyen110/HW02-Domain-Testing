# FR-08: Thanh toán (Checkout) — Boundary Value Analysis Report

## 1. Feature Overview

| Item         | Detail                           |
| ------------ | -------------------------------- |
| Feature ID   | FR-08                            |
| Feature Name | Thanh toán (Checkout)            |
| URL          | `http://localhost:5173/checkout` |
| Technique    | Boundary Value Analysis (BVA)    |

---

## 2. Xác định các biên (Boundaries)

| Biến                    | Ràng buộc                                                  | Nguồn           |
| ----------------------- | ---------------------------------------------------------- | --------------- |
| `cart_items`            | Số lượng sản phẩm trong giỏ ≥ 1 mới checkout được          | Spec + observed |
| `total_amount`          | Phải > 0, do backend tính — không chấp nhận giá trị client | Spec            |
| `quantity` mỗi sản phẩm | Số lượng từng sản phẩm ≥ 1                                 | Observed        |

---

## 3. Phân tích biên từng biến

### 3.1 Biến `cart_items` — Số lượng sản phẩm trong giỏ

| Điểm biên                | Số sản phẩm    | Tên              | Kết quả mong đợi                  |
| ------------------------ | -------------- | ---------------- | --------------------------------- |
| **On-point (biên dưới)** | **0 sản phẩm** | Giỏ rỗng         | Không cho checkout                |
| Just above boundary      | 1 sản phẩm     | Tối thiểu hợp lệ | Cho phép checkout                 |
| In-range                 | 2+ sản phẩm    | Bình thường      | Cho phép checkout, tính đúng tổng |

---

### 3.2 Biến `total_amount` — Tổng tiền

| Điểm biên                     | Giá trị                | Kết quả mong đợi                   |
| ----------------------------- | ---------------------- | ---------------------------------- |
| Below boundary                | `total_amount < 0`     | Backend từ chối / tính lại         |
| **On-point**                  | **`total_amount = 0`** | Backend từ chối / tính lại         |
| Just above boundary           | `total_amount = 1đ`    | Backend chấp nhận nếu đúng với giỏ |
| Client sửa thành giá trị khác | Bất kỳ                 | Backend bỏ qua, tự tính lại        |

---

### 3.3 Biến `quantity` — Số lượng từng sản phẩm trong giỏ

| Điểm biên                | Giá trị           | Kết quả mong đợi         |
| ------------------------ | ----------------- | ------------------------ |
| **On-point (biên dưới)** | **0 sản phẩm**    | Không hợp lệ             |
| Just above boundary      | 1 sản phẩm        | Hợp lệ, tính đúng giá    |
| In-range                 | 2, 5, 10 sản phẩm | Tính đúng giá × số lượng |

---

## 4. Test Case Table

| TC ID  | Mô tả                                              | Input               | Biên liên quan           | Kết quả mong đợi                                 | Kết quả thực tế                                                              | Pass/Fail |
| ------ | -------------------------------------------------- | ------------------- | ------------------------ | ------------------------------------------------ | ---------------------------------------------------------------------------- | --------- |
| BVA-01 | Giỏ hàng rỗng (0 sản phẩm)                         | cart = 0 items      | **On-point cart=0**      | Không cho checkout, báo giỏ trống                | Hiển thị thông báo "Giỏ hàng trống"                                          | Pass      |
| BVA-02 | Giỏ hàng 1 sản phẩm — tối thiểu hợp lệ             | cart = 1 item       | Just above (cart=1)      | Cho checkout, hiển thị đúng 1 sản phẩm           | Hiển thị đúng 1 sản phẩm, cho phép thanh toán                                | Pass      |
| BVA-03 | Giỏ hàng 2 sản phẩm                                | cart = 2 items      | In-range (cart=2)        | Cho checkout, tổng tiền đúng                     | Hiển thị đúng 2 sản phẩm, cho phép thanh toán                                | Pass      |
| BVA-04 | total_amount = 0 do client gửi                     | client gửi total=0  | **On-point total=0**     | Backend từ chối hoặc tính lại đúng               | Backend chấp nhận sửa đổi từ client                                          | Fail      |
| BVA-05 | total_amount âm do client gửi                      | client gửi total=-1 | Below boundary total<0   | Backend từ chối hoặc tính lại đúng               | Backend chấp nhận, hiển thị số âm                                            | Fail      |
| BVA-06 | total_amount = 1đ hợp lệ                           | sản phẩm giá 1đ     | Just above total=1       | Backend chấp nhận nếu khớp giỏ hàng              | Backend chấp nhận mọi mức giá sửa đổi từ client                              | Fail      |
| BVA-07 | Client sửa total_amount thành giá trị nhỏ hơn thực | DevTools: sửa total | Client-side manipulation | Backend tính lại đúng, không dùng giá trị client | Backend chấp nhận mọi mức giá sửa đổi từ client, kể cả nhỏ hơn giá trị thực  | Fail      |
| BVA-08 | Client sửa total_amount thành giá trị lớn hơn thực | DevTools: sửa total | Client-side manipulation | Backend tính lại đúng, không dùng giá trị client | vBackend chấp nhận mọi mức giá sửa đổi từ client, kể cả lớn hơn giá trị thực | Fail      |
| BVA-09 | Số lượng sản phẩm = 0 trong giỏ                    | quantity = 0        | **On-point quantity=0**  | Không cho thêm vào giỏ hoặc tự xóa khỏi giỏ      | Sản phẩm tự động được xóa khỏi giỏ hàng                                      | Pass      |
| BVA-10 | Số lượng sản phẩm = 1                              | quantity = 1        | Just above quantity=1    | Tính đúng giá × 1                                | Backend tính đúng giá                                                        | Pass      |
| BVA-11 | Số lượng sản phẩm lớn (stress test)                | quantity = 999      | Far above boundary       | Tính đúng tổng tiền, không overflow              | Chưa thực thi                                                                | NA        |
