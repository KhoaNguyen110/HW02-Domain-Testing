# Agent Skill — Domain Testing & BVA

## Mô tả

Skill này giúp áp dụng kỹ thuật **Domain Testing** và **Boundary Value Analysis (BVA)**
một cách có hệ thống cho bất kỳ feature nào. Chỉ cần điền thông tin feature vào
phần `[BIẾN]` và paste vào AI tool.

---

## SKILL 01 — Domain Testing

```
Bạn là một QA Engineer chuyên nghiệp. Hãy áp dụng kỹ thuật Domain Testing
cho feature sau theo đúng các bước được mô tả.

=== THÔNG TIN FEATURE ===
Feature ID: [VD: FR-02]
Feature Name: [VD: Login and Account Lockout]
URL: [VD: http://localhost:5173/login]
Spec:
[Paste toàn bộ spec của feature vào đây]

Observed behavior (những gì đã khám phá khi chạy app thực tế):
[Mô tả những gì bạn thấy khi test thủ công, VD:
- Form có 2 field: email và password
- Validate bắt buộc nhập khi submit
- Bị khóa sau 3 lần sai
- ...]

=== YÊU CẦU ===
Hãy thực hiện Domain Testing theo đúng 3 bước sau:

BƯỚC 1 — XÁC ĐỊNH BIẾN INPUT
- Liệt kê tất cả các biến input của feature
- Với mỗi biến: nêu tên, kiểu dữ liệu, mô tả

BƯỚC 2 — PHÂN VÙNG MIỀN (Domain Partitioning)
- Với mỗi biến, chia thành các miền: Valid / Invalid / Edge
- Mỗi miền cần có: tên miền, loại, điều kiện, ví dụ cụ thể
- Trình bày dưới dạng bảng Markdown

BƯỚC 3 — THIẾT KẾ TEST CASES
- Chọn ít nhất 1 test case đại diện cho mỗi miền
- Kết hợp các miền để tạo test scenario thực tế
- Trình bày dưới dạng bảng Markdown với các cột:
  TC ID | Mô tả | [các biến input] | Kết quả mong đợi | Kết quả thực tế | Pass/Fail
- Cột "Kết quả thực tế" và "Pass/Fail" để trống (human sẽ điền sau khi test)

BƯỚC 4 — BUG REPORTS
- Dựa trên observed behavior, xác định những điểm không khớp với spec
- Với mỗi bug: Bug ID, Severity, Priority, Summary, Steps, Expected, Actual

BƯỚC 5 — AI GAP ANALYSIS
- Liệt kê những test cases hoặc bugs mà AI có thể bỏ sót
- Giải thích lý do tại sao AI bỏ sót

=== OUTPUT FORMAT ===
Trả về file Markdown hoàn chỉnh theo cấu trúc:
# [Feature ID]: [Feature Name] — Domain Testing Report
## 1. Feature Overview
## 2. Domain Testing — Step-by-step
### Bước 1: Xác định biến input
### Bước 2: Phân vùng miền
### Bước 3: Chọn test case đại diện
## 3. Test Case Table
## 4. Bug Reports
## 5. AI Gap Analysis
## 6. Test Summary
```

---

## SKILL 02 — Boundary Value Analysis (BVA)

```
Bạn là một QA Engineer chuyên nghiệp. Hãy áp dụng kỹ thuật Boundary Value
Analysis (BVA) cho feature sau theo đúng các bước được mô tả.

=== THÔNG TIN FEATURE ===
Feature ID: [VD: FR-02]
Feature Name: [VD: Login and Account Lockout]
URL: [VD: http://localhost:5173/login]
Spec:
[Paste toàn bộ spec của feature vào đây]

Observed behavior:
[Mô tả những gì đã quan sát khi chạy app thực tế]

Kết quả Domain Testing (nếu đã có):
[Tóm tắt các biến input và miền đã xác định ở Domain Testing]

=== YÊU CẦU ===
Hãy thực hiện BVA theo đúng các bước sau:

BƯỚC 1 — XÁC ĐỊNH CÁC BIÊN (Boundaries)
- Liệt kê tất cả ràng buộc có giá trị biên rõ ràng
- Với mỗi ràng buộc: nêu biến, điều kiện, nguồn (spec/observed)

BƯỚC 2 — PHÂN TÍCH BIÊN TỪNG BIẾN
- Với mỗi biến có biên, vẽ trục số minh họa dạng:
  INVALID | VALID
  [giá trị] | [giá trị]
- Xác định các điểm biên:
  + Below lower bound (dưới biên dưới)
  + On-point lower bound (đúng biên dưới) ← quan trọng nhất
  + Just above lower bound (trên biên dưới)
  + Just below upper bound (dưới biên trên)
  + On-point upper bound (đúng biên trên) ← quan trọng nhất
  + Just above upper bound (trên biên trên)

BƯỚC 3 — THIẾT KẾ TEST CASES
- Mỗi điểm biên = 1 test case
- Trình bày dưới dạng bảng Markdown với các cột:
  TC ID | Mô tả | Input | Biên liên quan | Kết quả mong đợi | Kết quả thực tế | Pass/Fail
- Cột "Kết quả thực tế" và "Pass/Fail" để trống

BƯỚC 4 — BUG REPORTS
- Dựa trên observed behavior, xác định lỗi tại các điểm biên
- Đặc biệt chú ý: off-by-one errors tại on-point

BƯỚC 5 — AI GAP ANALYSIS
- Những biên nào AI không thể tự test được (timing, network, UI)
- Lý do cụ thể

=== OUTPUT FORMAT ===
Trả về file Markdown hoàn chỉnh theo cấu trúc:
# [Feature ID]: [Feature Name] — BVA Report
## 1. Feature Overview
## 2. Xác định các biên
## 3. Phân tích biên từng biến
## 4. Test Case Table
## 5. Bug Reports
## 6. AI Gap Analysis
## 7. Test Summary
```

---

## SKILL 03 — Security Test Supplement

```
Dựa trên spec và các biến input đã xác định, hãy bổ sung thêm test cases
cho các yêu cầu bảo mật sau:

Feature ID: [Feature ID]
Spec: [Spec]
Security Requirements:
- SEC-03: API Admin phải kiểm tra role=admin trong Token
- SEC-04: Dữ liệu user nhập phải được escape, không dùng innerHTML
- SEC-05: Truy vấn CSDL phải dùng Parameterized Query
- SEC-06: API cập nhật profile không cho đổi trường role từ client

Với mỗi security requirement liên quan đến feature này:
1. Thiết kế test case cụ thể
2. Mô tả các bước test (bao gồm DevTools/Console nếu cần)
3. Nêu kết quả mong đợi
```

---

## Hướng dẫn sử dụng

### Quy trình chuẩn cho mỗi feature:

```
Bước 1: Khám phá app thực tế
        → Ghi lại observed behavior

Bước 2: Paste SKILL 01 vào Claude/ChatGPT
        → Điền Feature ID, Spec, Observed behavior
        → Nhận domain-testing.md

Bước 3: Paste SKILL 02 vào Claude/ChatGPT
        → Thêm kết quả Domain Testing vào input
        → Nhận bva.md

Bước 4: Paste SKILL 03 nếu feature có liên quan bảo mật
        → Nhận thêm security test cases

Bước 5: Human review
        → Chạy test cases trên app
        → Điền kết quả thực tế
        → Ghi nhận bugs mới phát hiện
```
