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

```
      INVALID (không checkout được)  │  VALID (cho checkout)
                [0]                  │       [1 .. ∞]
─────────────────────────────────────────────────────────
              0          │     1     │    2    3   ...
                         ↑
                     on-point (biên dưới)
```

| Điểm biên                | Số sản phẩm    | Tên              | Kết quả mong đợi                  |
| ------------------------ | -------------- | ---------------- | --------------------------------- |
| **On-point (biên dưới)** | **0 sản phẩm** | Giỏ rỗng         | Không cho checkout                |
| Just above boundary      | 1 sản phẩm     | Tối thiểu hợp lệ | Cho phép checkout                 |
| In-range                 | 2+ sản phẩm    | Bình thường      | Cho phép checkout, tính đúng tổng |

---

### 3.2 Biến `total_amount` — Tổng tiền

```
         INVALID          │         VALID
      (≤ 0 hoặc bị sửa)   │        (> 0, backend tính)
──────────────────────────────────────────────────────
  ... -1    0    │    1    │    2   ...  ∞
                 ↑
             on-point
```

| Điểm biên                     | Giá trị                | Kết quả mong đợi                   |
| ----------------------------- | ---------------------- | ---------------------------------- |
| Below boundary                | `total_amount < 0`     | Backend từ chối / tính lại         |
| **On-point**                  | **`total_amount = 0`** | Backend từ chối / tính lại         |
| Just above boundary           | `total_amount = 1đ`    | Backend chấp nhận nếu đúng với giỏ |
| Client sửa thành giá trị khác | Bất kỳ                 | Backend bỏ qua, tự tính lại        |

---

### 3.3 Biến `quantity` — Số lượng từng sản phẩm trong giỏ

```
       INVALID        │      VALID
          [0]         │     [1 .. max]
──────────────────────────────────────
     0     │    1     │    2   3  ...
           ↑
       on-point
```

| Điểm biên                | Giá trị           | Kết quả mong đợi         |
| ------------------------ | ----------------- | ------------------------ |
| **On-point (biên dưới)** | **0 sản phẩm**    | Không hợp lệ             |
| Just above boundary      | 1 sản phẩm        | Hợp lệ, tính đúng giá    |
| In-range                 | 2, 5, 10 sản phẩm | Tính đúng giá × số lượng |

---

## 4. Test Case Table

| TC ID  | Mô tả                                              | Input               | Biên liên quan           | Kết quả mong đợi                                 | Kết quả thực tế | Pass/Fail |
| ------ | -------------------------------------------------- | ------------------- | ------------------------ | ------------------------------------------------ | --------------- | --------- |
| BVA-01 | Giỏ hàng rỗng (0 sản phẩm)                         | cart = 0 items      | **On-point cart=0**      | Không cho checkout, báo giỏ trống                |                 |           |
| BVA-02 | Giỏ hàng 1 sản phẩm — tối thiểu hợp lệ             | cart = 1 item       | Just above (cart=1)      | Cho checkout, hiển thị đúng 1 sản phẩm           |                 |           |
| BVA-03 | Giỏ hàng 2 sản phẩm                                | cart = 2 items      | In-range (cart=2)        | Cho checkout, tổng tiền đúng                     |                 |           |
| BVA-04 | total_amount = 0 do client gửi                     | client gửi total=0  | **On-point total=0**     | Backend từ chối hoặc tính lại đúng               |                 |           |
| BVA-05 | total_amount âm do client gửi                      | client gửi total=-1 | Below boundary total<0   | Backend từ chối hoặc tính lại đúng               |                 |           |
| BVA-06 | total_amount = 1đ hợp lệ                           | sản phẩm giá 1đ     | Just above total=1       | Backend chấp nhận nếu khớp giỏ hàng              |                 |           |
| BVA-07 | Client sửa total_amount thành giá trị nhỏ hơn thực | DevTools: sửa total | Client-side manipulation | Backend tính lại đúng, không dùng giá trị client |                 |           |
| BVA-08 | Client sửa total_amount thành giá trị lớn hơn thực | DevTools: sửa total | Client-side manipulation | Backend tính lại đúng, không dùng giá trị client |                 |           |
| BVA-09 | Số lượng sản phẩm = 0 trong giỏ                    | quantity = 0        | **On-point quantity=0**  | Không cho thêm vào giỏ hoặc tự xóa khỏi giỏ      |                 |           |
| BVA-10 | Số lượng sản phẩm = 1                              | quantity = 1        | Just above quantity=1    | Tính đúng giá × 1                                |                 |           |
| BVA-11 | Số lượng sản phẩm lớn (stress test)                | quantity = 999      | Far above boundary       | Tính đúng tổng tiền, không overflow              |                 |           |

---

## 5. Bug Reports (phát hiện qua BVA)

### BUG-01: total_amount = 0 có thể được chấp nhận (cần verify)

| Field             | Detail                                                                                                                                  |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| **Bug ID**        | BUG-01                                                                                                                                  |
| **Phát hiện tại** | BVA-04                                                                                                                                  |
| **Severity**      | Critical                                                                                                                                |
| **Priority**      | High                                                                                                                                    |
| **Summary**       | Tại on-point `total_amount=0`, cần verify backend có tính lại không hay chấp nhận giá trị 0 từ client                                   |
| **Steps**         | 1. Mở DevTools → Network <br> 2. Thêm sản phẩm vào giỏ <br> 3. Intercept request checkout <br> 4. Sửa `total_amount = 0` <br> 5. Submit |
| **Expected**      | Backend tính lại đúng, không chấp nhận 0                                                                                                |
| **Actual**        | Cần verify                                                                                                                              |
| **Screenshot**    | `screenshots/bug-01-total-zero.png`                                                                                                     |

---

### BUG-02 (confirmed): Tổng tiền có thể sửa trực tiếp trên UI

| Field             | Detail                                                                                             |
| ----------------- | -------------------------------------------------------------------------------------------------- |
| **Bug ID**        | BUG-02                                                                                             |
| **Phát hiện tại** | BVA-07, BVA-08                                                                                     |
| **Severity**      | Critical                                                                                           |
| **Priority**      | High                                                                                               |
| **Summary**       | Field tổng tiền trên UI là editable — vi phạm spec "không cho phép người dùng chỉnh sửa trực tiếp" |
| **Expected**      | Field readonly                                                                                     |
| **Actual**        | Field có thể chỉnh sửa                                                                             |
| **Screenshot**    | `screenshots/bug-02-editable-total.png`                                                            |

---

## 6. AI Gap Analysis

| Gap                               | Mô tả                              | Lý do AI bỏ sót                                   |
| --------------------------------- | ---------------------------------- | ------------------------------------------------- |
| BVA-07/08 (DevTools manipulation) | Test qua intercept network request | AI không thể dùng DevTools                        |
| BUG-02 (editable field)           | Field UI là input thay vì readonly | AI không render UI thực tế                        |
| BVA-11 (quantity=999)             | Overflow hoặc performance          | AI không nghĩ đến stress test nếu không được nhắc |

---

## 7. Test Summary

| Metric               | Value                      |
| -------------------- | -------------------------- |
| Total BVA test cases | 11                         |
| Executed             |                            |
| Passed               |                            |
| Failed               |                            |
| Not executed         |                            |
| Bugs found           | 1 confirmed + 1 cần verify |
