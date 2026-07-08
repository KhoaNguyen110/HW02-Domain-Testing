# AI Audit Report — HW02 Domain Testing

## Khai báo sử dụng AI

"I use AI tools for the following tasks in this assignment."

---

## Thông tin chung

| Item                | Detail                      |
| ------------------- | --------------------------- |
| **Sinh viên**       | Nguyễn Đăng Khoa            |
| **MSSV**            | 23127393                    |
| **Môn học**         | Kiểm thử phần mềm           |
| **Bài tập**         | HW02 — Domain Testing & BVA |
| **AI Tool sử dụng** | Claude (claude.ai)          |

---

## Log các lần tương tác với AI

---

### Interaction #01

| Field        | Detail                                    |
| ------------ | ----------------------------------------- |
| **Tool**     | Claude (claude.ai)                        |
| **Ngày giờ** | [20/6/2026 10:00]                         |
| **Mục đích** | Tìm hiểu cấu trúc bài và setup môi trường |

**Prompt:**

```
Đọc qua yêu cầu HW2 tóm tắt giúp tôi hiểu về cấu trúc bài làm, cần làm gì và set up cây thư mục cũng như các môi trường cần thiết
```

**AI Output (tóm tắt):**

```
Tóm tắt HW02 – Domain Testing
Bạn cần làm gì?
Chọn 4 features từ ứng dụng EShop (mỗi pool 1 feature, không trùng với thành viên nhóm):

Pool A: FR-01 đến FR-06 (Auth, Categories, Products)
Pool B: FR-07 đến FR-11 (Cart, Checkout)
Pool C: FR-12 đến FR-19 (Web Admin)
Pool D: Mobile App


Với mỗi feature, làm 4 việc:

Domain Testing — Áp dụng kỹ thuật domain testing để thiết kế test cases, giải thích từng bước
Boundary Value Analysis (BVA) — Áp dụng BVA, thiết kế test cases, giải thích từng bước
AI Gap Analysis — Báo cáo những test cases/bugs mà AI bỏ sót, giải thích tại sao
Bug Reporting — Báo cáo bugs trong Markdown report + GitHub Issues (kèm screenshot)


Deliverables cần nộp (file .zip):
FileNội dungMain report (MD + PDF)Domain Testing + BVA reportBug reportScreenshots từ GitHub IssuesAI Critique (200–300 từ)Phê bình AI: sai gì, thiếu gì, tại saoAI Audit Report (MD + PDF)Log toàn bộ quá trình dùng AIGit commit logText fileREADME.mdBảng self-assessment + test summary

Lưu ý quan trọng:

Tên file: <StudentID>_HW02_AI_DomainTesting_<SelfAssessedGrade>.zip
Dùng AI theo kiểu guided (từng bước), không phải "generate hết một lần"
Phải review lại mọi output của AI — nộp raw output = không chấp nhận
Có thể build Agent Skills để tái sử dụng (bonus 10 điểm)
Nộp trễ = 0 điểm, thiếu tài liệu = 0 điểm

Gợi ý cấu trúc chuẩn

Bước 1: Setup môi trường
1. Tạo GitHub Repo
Vào github.com/new và tạo repo với cấu hình:

Repository name: HW02-Domain-Testing
Visibility: Private (hoặc Public tùy yêu cầu lớp)
Initialize: ✅ Add a README.md

Sau đó clone về máy:
bashgit clone https://github.com/<your-username>/HW02-Domain-Testing.git
cd HW02-Domain-Testing

2. Cấu trúc thư mục
Tạo theo cấu trúc này:
HW02-Domain-Testing/
│
├── README.md                          # Self-assessment table + test summary
│
├── reports/
│   ├── FR-XX_FeatureName/             # Mỗi feature 1 thư mục (x4)
│   │   ├── domain-testing.md          # Báo cáo Domain Testing
│   │   ├── bva.md                     # Báo cáo BVA
│   │   └── screenshots/               # Ảnh chụp bug
│   │
│   ├── FR-XX_FeatureName/
│   ├── FR-XX_FeatureName/
│   └── FR-XX_FeatureName/
│
├── bug-reports/
│   └── bugs.md                        # Tổng hợp tất cả bugs
│
├── ai-audit/
│   ├── ai-audit-report.md             # Log toàn bộ quá trình dùng AI
│   └── ai-critique.md                 # Bài viết phê bình AI (200-300 từ)
│
├── git-log/
│   └── git-commit-log.txt             # Export git log ra đây
│
└── submission/                        # Chứa file .zip cuối cùng

3. Tạo thư mục bằng lệnh
Chạy trong terminal (đang ở trong repo):
bashmkdir -p reports/FR-01_Registration/screenshots
mkdir -p reports/FR-07_ShoppingCart/screenshots
mkdir -p reports/FR-14_CategoryManagement/screenshots
mkdir -p reports/FR-Mobile/screenshots
mkdir -p bug-reports
mkdir -p ai-audit
mkdir -p git-log
mkdir -p submission

touch reports/FR-01_Registration/domain-testing.md
touch reports/FR-01_Registration/bva.md
touch bug-reports/bugs.md
touch ai-audit/ai-audit-report.md
touch ai-audit/ai-critique.md

Thay tên thư mục theo 4 features bạn chọn thực tế.


4. Commit đầu tiên
bashgit add .
git commit -m "chore: initial project structure setup"
git push origin main

5. Bật GitHub Issues
Vào repo → Settings → kéo xuống phần Features → bật Issues
Đây là nơi bạn sẽ báo cáo bugs sau này.
```

---

### Interaction #02

| Field        | Detail                                  |
| ------------ | --------------------------------------- |
| **Tool**     | Claude (claude.ai)                      |
| **Ngày giờ** | [20/6/2026 11:00]                       |
| **Mục đích** | Thiết kế Domain Testing cho FR-02 Login |

**Prompt:**

```
Bạn là một QA Engineer chuyên nghiệp. Hãy áp dụng kỹ thuật Domain Testing
cho feature sau theo đúng các bước được mô tả.

=== THÔNG TIN FEATURE ===
Feature ID: FR-02
Feature Name: Login and Account Lockout
URL: http://localhost:5173/login
Spec:
### FR-02: Đăng nhập & Khóa tài khoản
- Người dùng nhập Email và Mật khẩu.
- Sau mỗi lần đăng nhập sai, hệ thống tăng bộ đếm lên **đúng 1 đơn vị**.
- Nếu đăng nhập sai từ **3 lần trở lên** liên tiếp, tài khoản bị tạm khóa **30 giây** (môi trường demo). Hệ thống trả về thông báo lỗi phù hợp; không để lộ chi tiết nguyên nhân.
- Đăng nhập thành công trả về JWT Token. Token được lưu phía client và gửi kèm tất cả các yêu cầu có xác thực qua header `Authorization: Bearer <token>`.
- Trường email phải dùng `type="email"` (có validate HTML5 format).

Observed behavior (những gì đã khám phá khi chạy app thực tế):
- Title ở trang đăng nhập có nội dung là "Đăng ký"
- Form có 2 field: Username và password
- Field Username sai title vì đúng phải là Email
- Validate bắt buộc nhập email và password khi submit
- Bị khóa sau 2 lần sai và không thông báo đến người dùng rõ ràng
- Tài khoản bị khóa trong 3 phút tính từ lần nhập sai thứ 2
- Sau khi đăng nhập thành công, bộ đếm login-attempts được reset chính xác về 0
- Pass word không được ẩn khi người dùng nhập dữ liệu

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

**AI Output (tóm tắt):**

```
# FR-02: Login and Account Lockout — Domain Testing Report

## 1. Feature Overview

| Thuộc tính | Giá trị |
|---|---|
| Feature ID | FR-02 |
| Feature Name | Login and Account Lockout |
| URL | http://localhost:5173/login |
| Mô tả ngắn | Người dùng đăng nhập bằng Email + Mật khẩu. Sai từ 3 lần liên tiếp trở lên sẽ bị khóa tạm thời 30 giây (môi trường demo). Đăng nhập thành công trả về JWT Token dùng cho các request xác thực sau đó. |
| Kỹ thuật áp dụng | Domain Testing (Equivalence Partitioning + Boundary Value Analysis) |

---

## 2. Domain Testing — Step-by-step

### Bước 1: Xác định biến input

| # | Tên biến | Kiểu dữ liệu | Mô tả |
|---|---|---|---|
| 1 | `email` | string (nên là `type="email"`) | Định danh tài khoản đăng nhập, phải đúng định dạng email HTML5 |
| 2 | `password` | string (masked input) | Mật khẩu tài khoản, bắt buộc nhập |
| 3 | `failedAttemptCount` | integer (biến trạng thái hệ thống, không nhập trực tiếp nhưng bị điều khiển gián tiếp qua hành vi đăng nhập sai) | Bộ đếm số lần đăng nhập sai liên tiếp, quyết định việc khóa tài khoản |
| 4 | `lockoutElapsedTime` | integer / time (giây, biến trạng thái hệ thống) | Thời gian đã trôi qua kể từ thời điểm tài khoản bị khóa, quyết định thời điểm mở khóa |

> Lưu ý: biến #3 và #4 không phải input do người dùng gõ trực tiếp trên form, nhưng vẫn là **input logic** của hệ thống cần được domain-test vì chúng quyết định luồng nghiệp vụ khóa/mở tài khoản.

### Bước 2: Phân vùng miền

#### 2.1 Biến `email`

| Miền | Loại | Điều kiện | Ví dụ |
|---|---|---|---|
| Email hợp lệ chuẩn | Valid | Đúng định dạng `local@domain.tld` | `user@example.com` |
| Email hợp lệ có ký tự đặc biệt hợp lệ | Valid | Có dấu chấm, dấu `+`, gạch dưới trong local-part | `user.name+tag@example.com` |
| Rỗng | Invalid | Không nhập gì | `""` |
| Thiếu ký tự `@` | Invalid | Không có `@` | `userexample.com` |
| Thiếu domain / TLD | Invalid | Có `@` nhưng không có domain hợp lệ | `user@`, `user@.com` |
| Chứa khoảng trắng | Invalid | Có space ở đầu/giữa/cuối | `" user@example.com"` |
| Ký tự không hợp lệ | Invalid | Chứa ký tự bị cấm bởi RFC 5322 (khi validate) | `user@@example.com` |
| Độ dài tối đa (boundary) | Edge | Email dài đúng 254 ký tự (giới hạn RFC 5321) | 254-char email |
| Vượt độ dài tối đa | Edge/Invalid | 255 ký tự trở lên | 255-char email |
| Chữ hoa/thường lẫn lộn | Edge | Domain/local-part viết hoa | `User@EXAMPLE.com` |
| Email không tồn tại nhưng đúng format | Edge | Format hợp lệ nhưng tài khoản không tồn tại trong hệ thống | `notexist@example.com` |

#### 2.2 Biến `password`

| Miền | Loại | Điều kiện | Ví dụ |
|---|---|---|---|
| Mật khẩu đúng của tài khoản | Valid | Khớp với mật khẩu đã đăng ký | `Passw0rd!` |
| Mật khẩu sai | Invalid | Không khớp mật khẩu đã đăng ký | `WrongPass1` |
| Rỗng | Invalid | Không nhập gì | `""` |
| Chỉ chứa khoảng trắng | Invalid/Edge | Toàn space | `"    "` |
| Độ dài rất ngắn (1 ký tự) | Edge | Boundary dưới | `"a"` |
| Độ dài rất dài | Edge | Chuỗi vượt giới hạn thông thường (vd 256+ ký tự) | chuỗi 300 ký tự |
| Ký tự đặc biệt / unicode | Edge | Emoji, ký tự tiếng Việt có dấu, SQL-injection-like string | `"P@ss'; DROP TABLE--"` |

#### 2.3 Biến `failedAttemptCount`

| Miền | Loại | Điều kiện | Ví dụ |
|---|---|---|---|
| Chưa bị khóa | Valid | Số lần sai liên tiếp = 0, 1, hoặc 2 | 0, 1, 2 |
| Boundary chuyển trạng thái | Edge | Đúng lần sai thứ 3 liên tiếp — theo spec đây là ngưỡng khóa | 3 |
| Đã bị khóa, tiếp tục thử | Edge/Invalid | Số lần sai > 3 trong lúc đang bị khóa | 4, 5 |
| Đếm bị ngắt quãng bởi lần đăng nhập đúng | Edge | Sai 2 lần rồi đăng nhập đúng → counter phải reset về 0 | 2 sai → 1 đúng → reset 0 |

#### 2.4 Biến `lockoutElapsedTime`

| Miền | Loại | Điều kiện | Ví dụ |
|---|---|---|---|
| Trong thời gian khóa | Valid (hệ thống từ chối đăng nhập) | 0s ≤ t < 30s kể từ lúc khóa | t = 15s |
| Boundary ngay trước khi mở khóa | Edge | t = 29s | 29s |
| Boundary đúng thời điểm mở khóa | Edge | t = 30s | 30s |
| Sau khi hết khóa | Valid (cho phép thử lại) | t > 30s | t = 31s |

---

### Bước 3: Chọn test case đại diện

Xem chi tiết bảng test case đầy đủ ở **Mục 3**. Nguyên tắc chọn:
- Mỗi miền (Valid/Invalid/Edge) của từng biến có ít nhất 1 test case đại diện.
- Kết hợp biến `email` × `password` × `failedAttemptCount` để tạo các scenario khóa tài khoản thực tế (login sai liên tiếp, boundary lần thứ 3, thử lại khi đang khóa, thử lại sau khi hết khóa).
- Có test case riêng cho JWT Token (lưu client-side, gửi kèm header `Authorization: Bearer <token>`).
- Có test case riêng cho thuộc tính `type="email"` của field.

---

## 3. Test Case Table

| TC ID | Mô tả | email | password | Trạng thái trước đó | Kết quả mong đợi | Kết quả thực tế | Pass/Fail |
|---|---|---|---|---|---|---|---|
| TC-01 | Đăng nhập thành công với email & password hợp lệ | `user@example.com` | `Passw0rd!` (đúng) | 0 lần sai | Đăng nhập thành công, trả về JWT Token, token được lưu client và gửi kèm header `Authorization: Bearer <token>` cho các request sau | | |
| TC-02 | Đăng nhập sai password lần 1 | `user@example.com` | `WrongPass1` | 0 lần sai | Đăng nhập thất bại, counter tăng lên 1, thông báo lỗi chung chung (không lộ nguyên nhân cụ thể) | | |
| TC-03 | Đăng nhập sai password lần 2 liên tiếp | `user@example.com` | `WrongPass2` | 1 lần sai | Counter tăng lên 2, tài khoản **chưa** bị khóa | | |
| TC-04 | Đăng nhập sai password lần 3 liên tiếp (boundary khóa) | `user@example.com` | `WrongPass3` | 2 lần sai | Counter = 3, tài khoản bị khóa tạm thời 30 giây, hệ thống trả thông báo lỗi phù hợp, không lộ chi tiết nguyên nhân | | |
| TC-05 | Thử đăng nhập khi tài khoản đang bị khóa (kể cả đúng password) | `user@example.com` | `Passw0rd!` (đúng) | Đang bị khóa, t = 10s | Hệ thống từ chối đăng nhập, thông báo tài khoản đang tạm khóa, không cho vào dù password đúng | | |
| TC-06 | Thử đăng nhập ngay tại boundary 29s kể từ lúc khóa | `user@example.com` | `Passw0rd!` | Đang bị khóa, t = 29s | Vẫn bị từ chối (chưa đủ 30s) | | |
| TC-07 | Thử đăng nhập ngay tại/khi qua boundary 30s kể từ lúc khóa | `user@example.com` | `Passw0rd!` | t = 30s–31s | Cho phép thử lại đăng nhập bình thường | | |
| TC-08 | Đăng nhập đúng sau 2 lần sai → counter phải reset về 0 | `user@example.com` | `Passw0rd!` (đúng) | 2 lần sai | Đăng nhập thành công, counter reset về 0 | | |
| TC-09 | Email để trống khi submit | `""` | `Passw0rd!` | - | Validate bắt buộc, hiển thị lỗi yêu cầu nhập email, không gọi API | | |
| TC-10 | Password để trống khi submit | `user@example.com` | `""` | - | Validate bắt buộc, hiển thị lỗi yêu cầu nhập password, không gọi API | | |
| TC-11 | Email sai định dạng HTML5 (thiếu @) | `userexample.com` | `Passw0rd!` | - | Trình duyệt/HTML5 validation chặn submit vì field phải có `type="email"` | | |
| TC-12 | Email đúng định dạng nhưng không tồn tại trong hệ thống | `notexist@example.com` | `AnyPass1` | - | Đăng nhập thất bại, thông báo lỗi chung (không tiết lộ email có tồn tại hay không) | | |
| TC-13 | Email hợp lệ có khoảng trắng đầu/cuối | `" user@example.com "` | `Passw0rd!` | - | Hệ thống nên trim khoảng trắng và xử lý như email hợp lệ, hoặc báo lỗi rõ ràng nếu không trim | | |
| TC-14 | Password chứa ký tự đặc biệt/unicode | `user@example.com` | `"Mật khẩu123!@#"` | - | Hệ thống xử lý đúng, không lỗi encoding, không bị injection | | |
| TC-15 | Kiểm tra field password bị ẩn ký tự khi nhập | `user@example.com` | `Passw0rd!` | - | Ký tự password phải hiển thị dạng `•••••` (masked), không hiển thị plaintext | | |
| TC-16 | Kiểm tra thuộc tính field email đúng `type="email"` | `user@example.com` | `Passw0rd!` | - | Inspect HTML: input field phải có `type="email"`, label/placeholder là "Email" | | |
| TC-17 | Kiểm tra title trang đăng nhập | - | - | - | Title trang phải thể hiện đúng là trang "Đăng nhập" | | |
| TC-18 | Kiểm tra JWT Token gửi kèm request xác thực | `user@example.com` | `Passw0rd!` (đúng) | - | Sau login, các request cần xác thực phải có header `Authorization: Bearer <token>` với token vừa nhận | | |
| TC-19 | Đăng nhập sai lần 4, 5 khi tài khoản đã bị khóa | `user@example.com` | `WrongPassX` | Đã bị khóa | Hệ thống tiếp tục từ chối do đang khóa, không tăng thêm counter một cách không kiểm soát (hoặc theo đúng thiết kế đã định nghĩa) | | |

---

## 4. Bug Reports

### BUG-01: Trang đăng nhập hiển thị sai Title ("Đăng ký" thay vì "Đăng nhập")
| Trường | Nội dung |
|---|---|
| Severity | Medium |
| Priority | Medium |
| Summary | Title của trang `/login` hiển thị "Đăng ký" thay vì "Đăng nhập", gây nhầm lẫn cho người dùng về chức năng của trang |
| Steps to Reproduce | 1. Truy cập `http://localhost:5173/login` <br> 2. Quan sát tiêu đề (title) của trang/form |
| Expected | Title phải thể hiện đúng là trang "Đăng nhập" |
| Actual | Title hiển thị là "Đăng ký" |

### BUG-02: Field bắt buộc là "Email" nhưng hiển thị/đặt tên là "Username", sai cả label lẫn kiểu input
| Trường | Nội dung |
|---|---|
| Severity | High |
| Priority | High |
| Summary | Theo spec, field đăng nhập phải là Email với `type="email"` để có validate HTML5 format, nhưng thực tế field được đặt tên/label là "Username", dẫn đến khả năng cao input không có `type="email"` và không kích hoạt validate định dạng email |
| Steps to Reproduce | 1. Truy cập `/login` <br> 2. Quan sát label field đầu tiên <br> 3. Inspect HTML để xem thuộc tính `type` của input |
| Expected | Field phải có label "Email" và thuộc tính `type="email"` |
| Actual | Field có label "Username", nghi ngờ `type` không phải `email` |

### BUG-03: Tài khoản bị khóa sau 2 lần đăng nhập sai thay vì 3 lần theo spec
| Trường | Nội dung |
|---|---|
| Severity | Critical |
| Priority | High |
| Summary | Spec yêu cầu khóa tài khoản khi đăng nhập sai **từ 3 lần trở lên liên tiếp**, nhưng hệ thống thực tế khóa tài khoản ngay sau lần sai thứ 2, sai lệch logic nghiệp vụ quan trọng ảnh hưởng đến trải nghiệm và bảo mật |
| Steps to Reproduce | 1. Truy cập `/login` <br> 2. Nhập sai password 2 lần liên tiếp với cùng 1 email <br> 3. Quan sát trạng thái tài khoản |
| Expected | Sau 2 lần sai, tài khoản chưa bị khóa; chỉ khóa khi đủ 3 lần sai liên tiếp trở lên |
| Actual | Tài khoản đã bị khóa ngay sau lần sai thứ 2 |

### BUG-04: Không có thông báo rõ ràng khi tài khoản bị khóa
| Trường | Nội dung |
|---|---|
| Severity | Medium |
| Priority | High |
| Summary | Spec yêu cầu "hệ thống trả về thông báo lỗi phù hợp" khi tài khoản bị khóa. Thực tế không có thông báo rõ ràng cho người dùng, khiến người dùng không hiểu vì sao không thể đăng nhập |
| Steps to Reproduce | 1. Làm cho tài khoản bị khóa (đăng nhập sai đủ số lần) <br> 2. Thử đăng nhập tiếp <br> 3. Quan sát thông báo hiển thị |
| Expected | Hiển thị thông báo phù hợp cho biết tài khoản đang tạm khóa, không lộ chi tiết kỹ thuật/nguyên nhân cụ thể |
| Actual | Không có thông báo rõ ràng đến người dùng |

### BUG-05: Thời gian khóa tài khoản là 3 phút thay vì 30 giây theo spec
| Trường | Nội dung |
|---|---|
| Severity | High |
| Priority | High |
| Summary | Spec quy định thời gian khóa demo là 30 giây, nhưng thực tế tài khoản bị khóa tới 3 phút, sai lệch đáng kể so với thiết kế, ảnh hưởng UX và việc kiểm thử |
| Steps to Reproduce | 1. Làm cho tài khoản bị khóa <br> 2. Đo thời gian từ lúc khóa đến khi có thể đăng nhập lại |
| Expected | Tài khoản được mở khóa sau đúng 30 giây |
| Actual | Tài khoản bị khóa trong 3 phút |

### BUG-06: Password không bị ẩn khi người dùng nhập
| Trường | Nội dung |
|---|---|
| Severity | Critical |
| Priority | High |
| Summary | Trường password hiển thị plaintext trong lúc nhập thay vì che dấu (mask) bằng dấu chấm/hoa thị, đây là lỗ hổng bảo mật/UX nghiêm trọng (shoulder surfing), field cần có `type="password"` |
| Steps to Reproduce | 1. Truy cập `/login` <br> 2. Nhập ký tự vào field password <br> 3. Quan sát ký tự hiển thị trên form |
| Expected | Ký tự password phải được ẩn dạng `•••••` khi nhập |
| Actual | Ký tự password hiển thị dạng plaintext, không được ẩn |

---

## 5. AI Gap Analysis

Những test case / bug mà AI có thể đã bỏ sót khi chỉ dựa vào spec + observed behavior được cung cấp:

| # | Gap | Lý do AI có thể bỏ sót |
|---|---|---|
| 1 | Kiểm thử **race condition**: nhiều request đăng nhập sai gửi đồng thời (song song) từ cùng 1 tài khoản | AI phân tích domain testing dựa trên input tuần tự, không có khả năng quan sát hành vi concurrency thực tế của backend nếu không có source code / architecture backend |
| 2 | Kiểm tra **counter có được lưu server-side hay bị thao túng được từ client** (vd sửa localStorage/cookie để reset counter) | Spec không đề cập cơ chế lưu trữ counter; AI không có quyền truy cập implementation nên không thể xác nhận đây có phải lỗ hổng bảo mật hay không |
| 3 | Kiểm thử **JWT Token**: token hết hạn, token bị sửa đổi (tampered), token bị dùng lại sau logout, token có được xác thực chữ ký (signature) đúng cách | Spec chỉ mô tả hành vi lưu & gửi token, không mô tả chính sách hết hạn/thu hồi, nên AI khó tự sinh test case về vòng đời token nếu không có thêm spec chi tiết |
| 4 | Kiểm thử **brute-force phân tán** (nhiều IP khác nhau tấn công cùng 1 tài khoản) hoặc **giới hạn theo IP** | Spec chỉ nói về khóa theo tài khoản, không đề cập rate-limit theo IP/thiết bị; đây là gap thường bị bỏ qua vì nằm ngoài phạm vi spec đưa ra |
| 5 | Độ chính xác **timing của boundary 30 giây** trong môi trường thực tế (network latency, clock skew giữa client/server) có thể gây flaky test | Domain testing lý thuyết giả định thời gian chính xác tuyệt đối; AI không thể lường trước độ trễ hệ thống thực tế khi chỉ đọc spec |
| 6 | **Accessibility**: label sai ("Username" thay vì "Email") ảnh hưởng đến người dùng dùng screen reader (đọc sai ngữ nghĩa field) | Đây là hệ quả gián tiếp của BUG-02 nhưng thuộc phạm trù test khác (a11y), dễ bị bỏ qua nếu không chủ động liên kết bug UI với tác động accessibility |
| 7 | Kiểm thử **giới hạn độ dài / chính sách độ mạnh của password** (min length, ký tự đặc biệt bắt buộc) | Spec không định nghĩa rule cụ thể cho password (chỉ nói "bắt buộc nhập"), nên đây là **spec gap** — AI chỉ liệt kê được ở mức "edge case chung", không thể test chính xác vì thiếu tiêu chí nghiệp vụ rõ ràng |
| 8 | Kiểm thử hành vi khi người dùng đăng nhập sai bằng **nhiều email khác nhau cùng lúc từ cùng 1 thiết bị** (cơ chế khóa theo email hay theo thiết bị/session) | Spec không làm rõ phạm vi khóa (theo tài khoản hay theo session/IP), nên AI không đủ dữ kiện để sinh test case chính xác, chỉ có thể nêu như một điểm cần làm rõ thêm với BA/PM |
| 9 | Kiểm thử **CSRF/CORS** khi gọi API login | Nằm ngoài phạm vi domain testing thuần túy (input-domain), thuộc kiểm thử bảo mật chuyên sâu hơn, cần công cụ/kỹ thuật khác ngoài Domain Testing |

---

## 6. Test Summary

| Hạng mục | Số lượng |
|---|---|
| Tổng số biến input được phân tích | 4 |
| Tổng số miền (domain) đã xác định | 30 |
| Tổng số test case thiết kế | 19 |
| Tổng số bug phát hiện từ observed behavior | 6 |
| Bug mức Critical | 2 (BUG-03, BUG-06) |
| Bug mức High | 2 (BUG-02, BUG-05) |
| Bug mức Medium | 2 (BUG-01, BUG-04) |
| Gap AI tự nhận diện (cần làm rõ thêm với BA/PM hoặc kiểm thử chuyên sâu hơn) | 9 |

**Kết luận:** Feature FR-02 hiện có nhiều sai lệch nghiêm trọng so với spec, đặc biệt ở 2 điểm liên quan trực tiếp đến bảo mật: (1) ngưỡng khóa tài khoản sai (2 lần thay vì 3 lần) và (2) password không được ẩn khi nhập. Cả hai đều nên được ưu tiên fix trước khi release. Ngoài ra, cần làm rõ thêm với BA/PM về chính sách độ mạnh password và phạm vi khóa tài khoản (theo account hay theo session/IP) để hoàn thiện bộ test case.
```

---

### Interaction #03

| Field        | Detail                       |
| ------------ | ---------------------------- |
| **Tool**     | Claude (claude.ai)           |
| **Ngày giờ** | [20/6/2026 13:30]            |
| **Mục đích** | Thiết kế BVA cho FR-02 Login |

**Prompt:**

```
Bạn là một QA Engineer chuyên nghiệp. Hãy áp dụng kỹ thuật Boundary Value
Analysis (BVA) cho feature sau theo đúng các bước được mô tả.

=== THÔNG TIN FEATURE ===
Feature ID: FR-02
Feature Name: Login and Account Lockout
URL: http://localhost:5173/login
Spec:
### FR-02: Đăng nhập & Khóa tài khoản
- Người dùng nhập Email và Mật khẩu.
- Sau mỗi lần đăng nhập sai, hệ thống tăng bộ đếm lên **đúng 1 đơn vị**.
- Nếu đăng nhập sai từ **3 lần trở lên** liên tiếp, tài khoản bị tạm khóa **30 giây** (môi trường demo). Hệ thống trả về thông báo lỗi phù hợp; không để lộ chi tiết nguyên nhân.
- Đăng nhập thành công trả về JWT Token. Token được lưu phía client và gửi kèm tất cả các yêu cầu có xác thực qua header `Authorization: Bearer <token>`.
- Trường email phải dùng `type="email"` (có validate HTML5 format).

Observed behavior:
- Title ở trang đăng nhập có nội dung là "Đăng ký"
- Form có 2 field: Username và password
- Field Username sai title vì đúng phải là Email
- Validate bắt buộc nhập email và password khi submit
- Bị khóa sau 2 lần sai và không thông báo đến người dùng rõ ràng
- Tài khoản bị khóa trong 3 phút tính từ lần nhập sai thứ 2
- Sau khi đăng nhập thành công, bộ đếm login-attempts được reset chính xác về 0
- Pass word không được ẩn khi người dùng nhập dữ liệu

Kết quả Domain Testing (nếu đã có):
Domain Testing — Step-by-step

#### Biến input

FR-02 có các biến input sau:

| Biến           | Kiểu    | Mô tả                                              |
| -------------- | ------- | -------------------------------------------------- |
| email          | String  | Địa chỉ email đăng nhập                            |
| password       | String  | Mật khẩu đăng nhập                                 |
| login_attempts | Integer | Số lần đăng nhập sai liên tiếp (ảnh hưởng lockout) |


#### Biến email

| Miền | Loại    | Điều kiện                                 | Ví dụ              |
| ---- | ------- | ----------------------------------------- | ------------------ |
| D1   | Valid   | Đúng format email HTML5, tồn tại trong DB | user@example.com   |
| D2   | Invalid | Sai format — không có @                   | userexample.com    |
| D3   | Invalid | Sai format — thiếu domain                 | user@              |
| D4   | Invalid | Sai format — chỉ có @                     | @gmail.com         |
| D5   | Invalid | Đúng format nhưng không tồn tại trong DB  | notexist@gmail.com |
| D6   | Edge    | Rỗng (empty)                              | ``                 |
| D7   | Edge    | Chỉ có khoảng trắng                       | `   `              |
| D8   | Edge    | Email chữ HOA                             | USER@GMAIL.COM     |

#### Biến password

| Miền | Loại    | Điều kiện                           | Ví dụ          |
| ---- | ------- | ----------------------------------- | -------------- |
| P1   | Valid   | Đúng password của tài khoản         | correctPass123 |
| P2   | Invalid | Sai password                        | wrongPass      |
| P3   | Edge    | Rỗng (empty)                        | ``             |
| P4   | Edge    | Có khoảng trắng đầu/cuối            | ` pass`        |
| P5   | Edge    | Password đúng nhưng khác hoa/thường | CORRECTPASS123 |

#### Biến login_attempts (số lần sai liên tiếp)

| Miền | Loại         | Giá trị                    | Hành vi mong đợi          |
| ---- | ------------ | -------------------------- | ------------------------- |
| A1   | Valid        | 0 (chưa sai lần nào)       | Không bị ảnh hưởng        |
| A2   | Valid        | 1 lần sai                  | Báo lỗi, cho thử lại      |
| A3   | Valid        | 2 lần sai                  | Báo lỗi, cho thử lại      |
| A4   | Boundary     | 3 lần sai                  | Kích hoạt lockout 30 giây |
| A5   | Invalid      | 4+ lần sai (trong lockout) | Vẫn bị khóa               |
| A6   | Post-lockout | Sau 30 giây                | Cho phép thử lại          |


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

**AI Output (tóm tắt):**

```
# FR-02: Login and Account Lockout — BVA Report

## 1. Feature Overview

| Thuộc tính | Giá trị |
|---|---|
| Feature ID | FR-02 |
| Feature Name | Login and Account Lockout |
| URL | http://localhost:5173/login |
| Kỹ thuật áp dụng | Boundary Value Analysis (BVA) — tập trung vào các giá trị biên (min/max/threshold) của các biến có ràng buộc số học/thời gian rõ ràng trong spec |
| Nguồn tham chiếu | Spec FR-02 + Observed behavior + Kết quả Domain Testing đã thực hiện trước đó |

> **Lưu ý phạm vi:** BVA chỉ áp dụng hiệu quả cho các biến có **ràng buộc số/thời gian có thứ tự** (ordinal). Trong FR-02, đó là `login_attempts` (ngưỡng khóa = 3) và thời gian khóa (ngưỡng mở khóa = 30 giây). Biến `email`/`password` dạng chuỗi tự do không có biên rõ ràng theo spec, ngoại trừ **độ dài** (được phân tích riêng ở mục 3.3 như một biên suy luận, không phải biên được spec định nghĩa tường minh).

---

## 2. Xác định các biên

| # | Biến | Điều kiện biên | Giá trị biên | Nguồn |
|---|---|---|---|---|
| B1 | `login_attempts` | "Đăng nhập sai từ **3 lần trở lên** liên tiếp → khóa tài khoản" | Threshold = **3** | Spec (tường minh) |
| B2 | `lockoutElapsedTime` | "Tạm khóa **30 giây** (môi trường demo)" | Threshold = **30 giây** | Spec (tường minh) |
| B3 | `email` (độ dài) | Chuẩn RFC 5321 cho địa chỉ email hợp lệ | Max = **254 ký tự** | Suy luận từ chuẩn HTML5 `type="email"` (**không** được spec nêu tường minh — cần confirm với BA) |
| B4 | `password` (độ dài) | Không có ràng buộc min/max được nêu trong spec | **Không xác định** | **Spec gap** — cần làm rõ với BA/PM trước khi thiết kế BVA đầy đủ cho password |

---

## 3. Phân tích biên từng biến

### 3.1 Biến `login_attempts` (B1 — quan trọng nhất, liên quan trực tiếp bug đã phát hiện)

```

        INVALID (chưa khóa)        |   VALID (đã khóa)

... 0 1 2 | 3 4 5 ...
↑
THRESHOLD (đúng biên)

```

| Điểm biên | Giá trị | Ý nghĩa |
|---|---|---|
| Below lower bound | `-1` | Không thể xảy ra qua UI (không có số lần sai âm) — chỉ test được nếu can thiệp API/DB |
| **On-point lower bound** | `0` | Tài khoản chưa từng sai lần nào |
| Just above lower bound | `1` | Sai lần đầu tiên |
| Just below upper bound | `2` | Sai lần thứ 2 — **theo spec vẫn CHƯA bị khóa** |
| **On-point upper bound** ⭐ | `3` | Sai lần thứ 3 — **theo spec đây là thời điểm khóa được kích hoạt** |
| Just above upper bound | `4` | Đã bị khóa, tiếp tục sai thêm |

### 3.2 Biến thời gian khóa (`lockoutElapsedTime`) (B2 — quan trọng nhất, liên quan bug thời lượng khóa)

```

        INVALID (vẫn đang khóa)    |   VALID (đã mở khóa)

... 0s 1s ... 29s | 30s 31s ...
↑
THRESHOLD (đúng biên)

```

| Điểm biên | Giá trị | Ý nghĩa |
|---|---|---|
| Below lower bound | `-1s` | Không thể xảy ra (thời gian không âm) — không testable qua UI |
| **On-point lower bound** | `0s` | Ngay thời điểm tài khoản vừa bị khóa |
| Just above lower bound | `1s` | Vừa mới bị khóa được 1 giây |
| Just below upper bound | `29s` | Còn 1 giây nữa mới được mở khóa — **phải vẫn còn bị từ chối** |
| **On-point upper bound** ⭐ | `30s` | Đúng thời điểm hết hạn khóa — **phải cho phép đăng nhập lại** |
| Just above upper bound | `31s` | Đã qua thời gian khóa — cho phép đăng nhập bình thường |

### 3.3 Biến độ dài `email` (B3 — biên suy luận từ chuẩn, không phải spec tường minh)

```

        VALID                       |   INVALID

... 253 ký tự | 255 ký tự ...
↑
254 ký tự (đúng biên, theo RFC 5321)

```

| Điểm biên | Giá trị | Ý nghĩa |
|---|---|---|
| Just below upper bound | `253 ký tự` | Vẫn hợp lệ theo chuẩn |
| **On-point upper bound** | `254 ký tự` | Giới hạn tối đa theo RFC 5321 |
| Just above upper bound | `255 ký tự` | Nên bị từ chối/không cho submit |

> Biên dưới (min length) của email không có ý nghĩa BVA riêng biệt ngoài trường hợp rỗng — trường hợp rỗng đã được xử lý trong Domain Testing (validate required), không lặp lại ở đây.

### 3.4 Biến độ dài `password` (B4)

**Không thể vẽ trục biên** vì spec không định nghĩa min/max length cho password. Đây là **gap cần escalate lên BA/PM** trước khi có thể thiết kế test case BVA hợp lệ cho biến này (xem chi tiết Mục 6).

---

## 4. Test Case Table

| TC ID | Mô tả | Input | Biên liên quan | Kết quả mong đợi | Kết quả thực tế | Pass/Fail |
|---|---|---|---|---|---|---|
| BVA-01 | Số lần sai âm (below lower bound) | `login_attempts = -1` (chỉ thực hiện được qua can thiệp API/DB, không qua UI) | B1 – below lower | N/A / không hợp lệ, hệ thống không cho phép trạng thái này tồn tại | | |
| BVA-02 | Tài khoản chưa từng sai lần nào (on-point lower) | `login_attempts = 0`, đăng nhập đúng | B1 – on-point lower | Đăng nhập thành công bình thường, không có cảnh báo khóa | | |
| BVA-03 | Sai lần đầu tiên (just above lower) | Email đúng, password sai lần 1 | B1 – just above lower | Counter = 1, báo lỗi sai thông tin, cho thử lại | | |
| BVA-04 | Sai lần thứ 2 liên tiếp (just below upper) | Email đúng, password sai lần 2 | B1 – just below upper | Counter = 2, **tài khoản CHƯA bị khóa**, cho thử lại | | |
| BVA-05 ⭐ | Sai lần thứ 3 liên tiếp (on-point upper — biên quan trọng nhất) | Email đúng, password sai lần 3 | B1 – on-point upper | Counter = 3, **tài khoản BỊ khóa ngay tại lần thứ 3**, hệ thống trả thông báo phù hợp | | |
| BVA-06 | Sai lần thứ 4 khi đã bị khóa (just above upper) | Email đúng, password sai lần 4 (trong lúc đang khóa) | B1 – just above upper | Vẫn bị từ chối do đang khóa, hành vi counter theo đúng thiết kế đã định nghĩa | | |
| BVA-07 | Thử đăng nhập ngay khi vừa bị khóa (on-point lower của thời gian khóa) | Đăng nhập đúng password ngay tại t = 0s sau khi bị khóa | B2 – on-point lower | Bị từ chối, thông báo tài khoản đang tạm khóa | | |
| BVA-08 | Thử đăng nhập ở t = 1s (just above lower) | t = 1s kể từ lúc khóa | B2 – just above lower | Vẫn bị từ chối | | |
| BVA-09 | Thử đăng nhập ở t = 29s (just below upper) | t = 29s kể từ lúc khóa | B2 – just below upper | Vẫn bị từ chối (chưa đủ 30s) | | |
| BVA-10 ⭐ | Thử đăng nhập đúng tại t = 30s (on-point upper — biên quan trọng nhất) | t = 30s kể từ lúc khóa | B2 – on-point upper | **Phải được phép đăng nhập lại** (khóa đã hết hạn đúng 30s) | | |
| BVA-11 | Thử đăng nhập ở t = 31s (just above upper) | t = 31s kể từ lúc khóa | B2 – just above upper | Đăng nhập bình thường | | |
| BVA-12 | Email dài 253 ký tự (just below upper) | Email hợp lệ, đủ 253 ký tự | B3 – just below upper | Chấp nhận, submit thành công | | |
| BVA-13 | Email dài đúng 254 ký tự (on-point upper) | Email hợp lệ, đủ 254 ký tự | B3 – on-point upper | Chấp nhận (đúng giới hạn RFC 5321) | | |
| BVA-14 | Email dài 255 ký tự (just above upper) | Email 255 ký tự | B3 – just above upper | Nên bị từ chối / báo lỗi vượt độ dài | | |

---

## 5. Bug Reports

### BUG-BVA-01: Off-by-one error nghiêm trọng tại biên `login_attempts` — khóa tài khoản sớm 1 lần so với spec
| Trường | Nội dung |
|---|---|
| Severity | Critical |
| Priority | High |
| Boundary liên quan | B1 – on-point upper (giá trị 3) |
| Summary | Spec định nghĩa rõ **on-point upper bound = 3** ("sai từ 3 lần trở lên") là thời điểm kích hoạt khóa. Thực tế hệ thống kích hoạt khóa tại `login_attempts = 2`, tức **sớm hơn 1 đơn vị** so với biên đã định nghĩa. Đây là lỗi kinh điển **off-by-one**, nhiều khả năng do điều kiện code dùng `>= 2` hoặc `> 1` thay vì `>= 3` |
| Steps to Reproduce | 1. Truy cập `/login` <br> 2. Nhập sai password 2 lần liên tiếp với cùng email <br> 3. Quan sát trạng thái tài khoản ngay sau lần sai thứ 2 |
| Expected (theo biên on-point upper) | Tại `login_attempts = 2` (just below upper), tài khoản **chưa** bị khóa. Chỉ khóa khi `login_attempts = 3` (on-point upper) |
| Actual | Tài khoản đã bị khóa ngay tại `login_attempts = 2` |

### BUG-BVA-02: Sai giá trị biên thời gian khóa — 3 phút thay vì đúng biên 30 giây
| Trường | Nội dung |
|---|---|
| Severity | High |
| Priority | High |
| Boundary liên quan | B2 – on-point upper (giá trị 30s) |
| Summary | Spec định nghĩa on-point upper bound của thời gian khóa là **30 giây**. Thực tế đo được thời gian khóa là **3 phút (180 giây)**, lệch **6 lần** so với biên đã định nghĩa. Đây không phải off-by-one mà là sai lệch giá trị cấu hình/hardcode nghiêm trọng |
| Steps to Reproduce | 1. Làm tài khoản bị khóa <br> 2. Bấm đồng hồ đo thời gian từ lúc khóa đến khi có thể đăng nhập lại thành công |
| Expected (on-point upper) | Tại t = 30s, tài khoản được mở khóa, cho phép đăng nhập lại |
| Actual | Tại t = 30s tài khoản **vẫn còn bị khóa**; phải chờ đến ~180s mới mở khóa |

### BUG-BVA-03: Không có thông báo tại thời điểm biên kích hoạt khóa (on-point upper của login_attempts)
| Trường | Nội dung |
|---|---|
| Severity | Medium |
| Priority | High |
| Boundary liên quan | B1 – on-point upper |
| Summary | Đúng tại thời điểm biên kích hoạt khóa (lần sai thứ 2 theo hành vi thực tế — lẽ ra phải là lần thứ 3 theo spec), spec yêu cầu "trả về thông báo lỗi phù hợp". Thực tế không có thông báo rõ ràng để người dùng biết tài khoản vừa bị khóa tại chính thời điểm biên đó |
| Steps to Reproduce | 1. Đăng nhập sai đến khi tài khoản bị khóa <br> 2. Quan sát thông báo ngay tại lần sai kích hoạt khóa |
| Expected | Thông báo rõ ràng nhưng không lộ chi tiết nguyên nhân (vd: "Tài khoản tạm thời bị khóa, vui lòng thử lại sau") xuất hiện ngay tại on-point upper |
| Actual | Không có thông báo rõ ràng |

> **Ghi chú:** Bug về password không bị ẩn (masked) khi nhập đã được ghi nhận ở báo cáo Domain Testing trước đó (BUG-06) — không thuộc phạm vi biên (boundary) nên không lặp lại chi tiết ở đây.

---

## 6. AI Gap Analysis

Những biên mà AI **không thể tự kiểm thử chắc chắn** chỉ bằng UI/black-box, kèm lý do cụ thể:

| # | Biên/Trường hợp | Lý do AI không thể tự test đầy đủ |
|---|---|---|
| 1 | `login_attempts = -1` (below lower bound) và `lockoutElapsedTime = -1s` | Các giá trị âm này **không thể tạo ra được thông qua thao tác UI thông thường** (không có cách nào để "giảm" số lần sai xuống dưới 0 qua giao diện). Muốn test thật sự cần can thiệp trực tiếp vào DB hoặc gọi API nội bộ — nằm ngoài khả năng của black-box UI testing thuần túy |
| 2 | Độ chính xác tuyệt đối tại on-point upper `30s` (BVA-10) | Trong môi trường thực tế có **network latency, độ trễ xử lý request, chênh lệch đồng hồ giữa client và server**. Test "chính xác tại giây thứ 30" trên thực tế rất khó đảm bảo 100% — có thể pass/fail flaky tùy vào độ trễ, AI không kiểm soát được yếu tố thời gian thực này khi chỉ đọc spec |
| 3 | Xác định nguyên nhân gốc của off-by-one (BUG-BVA-01) là do code dùng `>=`, `>` hay biến đếm khởi tạo sai | AI chỉ quan sát được **hành vi output** qua UI, không đọc được logic điều kiện thật trong source code, nên chỉ có thể suy đoán nguyên nhân kỹ thuật, không thể khẳng định chắc chắn dòng code nào gây lỗi |
| 4 | Biên độ dài email 254 ký tự (B3) có thực sự được hệ thống áp dụng hay không | Đây là biên **AI tự suy luận từ chuẩn RFC 5321**, không phải từ spec đã cho. Hệ thống thực tế có thể dùng giới hạn khác (vd giới hạn theo cột DB `varchar(100)`), nên test case BVA-12/13/14 chỉ có giá trị tham khảo, cần verify qua Inspect Element hoặc hỏi dev trước khi coi là bug thật nếu fail |
| 5 | Biên độ dài `password` (B4) — không thể thiết kế test case BVA vì spec không định nghĩa min/max | Đây là **gap từ chính spec**, không phải AI bỏ sót do năng lực; AI không thể "bịa" ra một con số biên khi tài liệu không cung cấp — cần được làm rõ với BA/PM trước |
| 6 | Race condition tại đúng biên (nhiều request đăng nhập sai gửi đồng thời ngay tại lần thứ 3) | Test đơn luồng qua UI không thể mô phỏng tình huống 2 request đến server **gần như đồng thời** ngay tại thời điểm biên, dẫn đến khả năng bypass hoặc double-increment counter — cần công cụ load-test/script chuyên dụng, ngoài phạm vi thao tác UI tuần tự |
| 7 | Việc counter có bị reset đúng ngay khi hết 30s hay chỉ reset khi có lần login tiếp theo | Spec không mô tả rõ cơ chế: mở khóa tự động theo thời gian (passive) hay chỉ mở khi có request login mới kiểm tra điều kiện (active check on next attempt). AI không có đủ thông tin backend để phân biệt 2 khả năng này, cần hỏi thêm dev/BA |

---

## 7. Test Summary

| Hạng mục | Số lượng |
|---|---|
| Tổng số biên (boundaries) xác định | 4 (B1, B2, B3, B4) |
| Biên có thể thiết kế BVA đầy đủ | 3 (B1, B2, B3) |
| Biên bị chặn do spec gap | 1 (B4 — password length) |
| Tổng số test case BVA thiết kế | 14 |
| Test case liên quan on-point boundary (quan trọng nhất) | 3 (BVA-05, BVA-10, BVA-13) |
| Bug phát hiện tại các điểm biên | 3 (BUG-BVA-01, 02, 03) |
| Bug mức Critical | 1 (BUG-BVA-01 — off-by-one tại on-point upper của login_attempts) |
| Bug mức High | 1 (BUG-BVA-02 — sai giá trị biên thời gian khóa) |
| Bug mức Medium | 1 (BUG-BVA-03 — thiếu thông báo tại on-point upper) |
| Gap AI tự nhận diện | 7 |

**Kết luận:** BVA đã xác nhận và khoanh vùng rõ **nguyên nhân kỹ thuật khả nghi (off-by-one)** cho bug khóa tài khoản sớm — đây là loại lỗi kinh điển mà kỹ thuật BVA đặc biệt hiệu quả trong việc phát hiện, so với Domain Testing chỉ dừng ở mức "phát hiện có sai lệch". Ưu tiên fix ngay **BUG-BVA-01** (on-point upper của `login_attempts`) và **BUG-BVA-02** (on-point upper của thời gian khóa) vì cả hai đều lệch trực tiếp so với giá trị biên tường minh trong spec. Cần làm rõ thêm với BA/PM về biên độ dài password (B4) và cơ chế mở khóa tự động (passive/active) trước khi hoàn thiện bộ test đầy đủ.
```

---

### Interaction #04

| Field        | Detail                                           |
| ------------ | ------------------------------------------------ |
| **Tool**     | Claude (claude.ai)                               |
| **Ngày giờ** | [1/7/2026 10:00]                                 |
| **Mục đích** | Thiết kế Domain Testing + BVA cho FR-08 Checkout |

**Prompt:**

```
Prompt cho Domain Testing:
Bạn là một QA Engineer chuyên nghiệp. Hãy áp dụng kỹ thuật Domain Testing cho feature sau theo đúng các bước được mô tả.

=== THÔNG TIN FEATURE ===
Feature ID: FR-08
Feature Name: Checkout
URL: http://localhost:5173/checkout
Spec:
### FR-08: Thanh toán (Checkout)
- Người dùng phải đăng nhập mới được vào trang checkout.
- Giỏ hàng phải có ít nhất 1 sản phẩm mới được phép checkout.
- Tổng tiền (total_amount) hiển thị trên UI là readonly, do backend tính toán dựa trên giỏ hàng thực tế — backend luôn tự tính lại tổng tiền khi nhận request checkout, không sử dụng giá trị total_amount do client gửi lên.
- Người dùng phải có đầy đủ thông tin giao hàng (địa chỉ, số điện thoại) trong profile mới được checkout thành công.
- Sau khi checkout thành công: giỏ hàng phải được xóa (rỗng), đơn hàng xuất hiện trong lịch sử đơn hàng.

Observed behavior (những gì đã khám phá khi chạy app thực tế):
- Sau khi thanh toán thành công, giỏ hàng vẫn còn nguyên sản phẩm, không bị xóa
- Field tổng tiền trên UI có thể chỉnh sửa trực tiếp (không phải readonly)
- Khi sửa total_amount qua DevTools/intercept request (kể cả = 0, số âm, thấp hơn hoặc cao hơn giá trị thực), backend vẫn chấp nhận và không tính lại
- Vẫn checkout thành công dù profile thiếu địa chỉ và/hoặc số điện thoại
- Lịch sử đơn hàng được cập nhật chính xác sau khi checkout thành công
- Với giỏ hàng có nhiều sản phẩm, tổng tiền hiển thị đúng bằng sum(giá × số lượng)

=== YÊU CẦU ===
Hãy thực hiện Domain Testing theo đúng 3 bước sau:

BƯỚC 1 — XÁC ĐỊNH BIẾN INPUT
- Liệt kê tất cả các biến input của feature (ví dụ: auth, cart, total_amount, profile)
- Với mỗi biến: nêu tên, kiểu dữ liệu, mô tả

BƯỚC 2 — PHÂN VÙNG MIỀN (Domain Partitioning)
- Với mỗi biến, chia thành các miền: Valid / Invalid / Edge
- Mỗi miền cần có: tên miền, loại, điều kiện, ví dụ cụ thể
- Trình bày dưới dạng bảng Markdown

BƯỚC 3 — THIẾT KẾ TEST CASES
- Chọn ít nhất 1 test case đại diện cho mỗi miền
- Kết hợp các miền để tạo test scenario thực tế (vd: đã đăng nhập + giỏ hàng có hàng + client sửa total_amount + profile thiếu thông tin)
- Trình bày dưới dạng bảng Markdown với các cột:
  TC ID | Mô tả | auth | cart | total_amount | profile | Kết quả mong đợi | Kết quả thực tế | Pass/Fail
- Cột "Kết quả thực tế" và "Pass/Fail" để trống (human sẽ điền sau khi test)

BƯỚC 4 — BUG REPORTS
- Dựa trên observed behavior, xác định những điểm không khớp với spec
- Với mỗi bug: Bug ID, Severity, Priority, Summary, Steps, Expected, Actual

BƯỚC 5 — AI GAP ANALYSIS
- Liệt kê những test cases hoặc bugs mà AI có thể bỏ sót
- Giải thích lý do tại sao AI bỏ sót

=== OUTPUT FORMAT ===
Trả về file Markdown hoàn chỉnh theo cấu trúc:
# FR-08: Checkout — Domain Testing Report
## 1. Feature Overview
## 2. Domain Testing — Step-by-step
### Bước 1: Xác định biến input
### Bước 2: Phân vùng miền
### Bước 3: Chọn test case đại diện
## 3. Test Case Table
## 4. Bug Reports
## 5. AI Gap Analysis
## 6. Test Summary

Prompt cho BVA:
Bạn là một QA Engineer chuyên nghiệp. Hãy áp dụng kỹ thuật Boundary Value Analysis (BVA) cho feature sau theo đúng các bước được mô tả.

=== THÔNG TIN FEATURE ===
Feature ID: FR-08
Feature Name: Checkout
URL: http://localhost:5173/checkout
Spec:
### FR-08: Thanh toán (Checkout)
- Người dùng phải đăng nhập mới được vào trang checkout.
- Giỏ hàng phải có ít nhất 1 sản phẩm mới được phép checkout (0 sản phẩm = từ chối).
- Tổng tiền (total_amount) là readonly, backend luôn tự tính lại dựa trên giỏ hàng thực tế, không dùng giá trị client gửi lên (kể cả 0, số âm, hoặc bất kỳ giá trị nào khác).
- Người dùng phải có đầy đủ địa chỉ + SĐT trong profile mới checkout thành công.

Observed behavior:
- Giỏ hàng 0 sản phẩm bị chặn, hiển thị thông báo giỏ hàng trống
- Giỏ hàng 1 sản phẩm vẫn checkout được bình thường
- total_amount = 0 do client gửi vẫn được backend chấp nhận
- total_amount âm do client gửi vẫn được backend chấp nhận
- Backend chấp nhận mọi giá trị total_amount client tự sửa qua DevTools, kể cả thấp hơn hoặc cao hơn giá trị thực tế của giỏ hàng
- Số lượng sản phẩm = 0 trong giỏ thì tự động bị xóa khỏi giỏ hàng
- Số lượng sản phẩm lớn (999) trong giỏ chưa được thực thi test

Kết quả Domain Testing (nếu đã có): [paste kết quả Domain Testing FR-08 vào đây]

=== YÊU CẦU ===
Hãy thực hiện BVA theo đúng các bước sau:

BƯỚC 1 — XÁC ĐỊNH CÁC BIÊN (Boundaries)
- Liệt kê tất cả ràng buộc có giá trị biên rõ ràng (vd: cart quantity, total_amount)
- Với mỗi ràng buộc: nêu biến, điều kiện, nguồn (spec/observed)

BƯỚC 2 — PHÂN TÍCH BIÊN TỪNG BIẾN
- Với mỗi biến có biên, vẽ trục số minh họa dạng:
  INVALID | VALID
  [giá trị] | [giá trị]
- Xác định các điểm biên:
  + Below lower bound
  + On-point lower bound ← quan trọng nhất
  + Just above lower bound
  + Just below upper bound
  + On-point upper bound ← quan trọng nhất
  + Just above upper bound

BƯỚC 3 — THIẾT KẾ TEST CASES
- Mỗi điểm biên = 1 test case
- Trình bày dưới dạng bảng Markdown với các cột:
  TC ID | Mô tả | Input | Biên liên quan | Kết quả mong đợi | Kết quả thực tế | Pass/Fail
- Cột "Kết quả thực tế" và "Pass/Fail" để trống

BƯỚC 4 — BUG REPORTS
- Dựa trên observed behavior, xác định lỗi tại các điểm biên
- Đặc biệt chú ý: off-by-one errors và client-side manipulation tại on-point

BƯỚC 5 — AI GAP ANALYSIS
- Những biên nào AI không thể tự test được (DevTools/network intercept, performance/stress test)
- Lý do cụ thể

=== OUTPUT FORMAT ===
Trả về file Markdown hoàn chỉnh theo cấu trúc:
# FR-08: Checkout — BVA Report
## 1. Feature Overview
## 2. Xác định các biên
## 3. Phân tích biên từng biến
## 4. Test Case Table
## 5. Bug Reports
## 6. AI Gap Analysis
## 7. Test Summary
```

**AI Output (tóm tắt):**

```
Tóm tắt kết quả AI đã sinh ra cho FR08

Domain Testing phát hiện 4 nhóm vấn đề chính: giỏ hàng không xóa sau checkout, tổng tiền có thể sửa trên UI, thiếu ràng buộc bắt buộc địa chỉ/SĐT khi checkout, và nghi ngờ backend không tính lại total_amount (ghi "cần verify" vì AI không thể thao tác DevTools).
BVA đã xác nhận (confirm) được nghi vấn còn bỏ ngỏ ở Domain Testing: bằng cách thiết kế test case tại các điểm biên total_amount = 0, âm, thấp hơn/cao hơn giá trị thực, BVA chứng minh backend hoàn toàn không tính lại tổng tiền — đây là lỗ hổng bảo mật nghiêm trọng (có thể checkout với giá 0đ).
Sau khi gộp 2 báo cáo và loại trùng, kết quả cuối: 4 bug duy nhất (tổng tiền editable trên UI, giỏ hàng không xóa, thiếu thông tin giao hàng vẫn checkout được, backend không tính lại total_amount — bug nghiêm trọng nhất, mức Critical).
Điểm mạnh của việc kết hợp 2 kỹ thuật: Domain Testing phát hiện "có vấn đề" nhưng dừng ở mức nghi ngờ với total_amount; BVA đi sâu vào từng giá trị biên cụ thể mới xác nhận chắc chắn được lỗi.
```

---

### Interaction #05

| Field        | Detail                                                      |
| ------------ | ----------------------------------------------------------- |
| **Tool**     | Claude (claude.ai)                                          |
| **Ngày giờ** | [1/7/2026 14:00]                                            |
| **Mục đích** | Thiết kế Domain Testing + BVA cho FR-14 Category Management |

**Prompt:**

```
Prompt cho Domain Testing:
Bạn là một QA Engineer chuyên nghiệp. Hãy áp dụng kỹ thuật Domain Testing cho feature sau theo đúng các bước được mô tả.

=== THÔNG TIN FEATURE ===
Feature ID: FR-14
Feature Name: Category Management (Admin)
URL: http://localhost:5173/admin/categories
Spec:
### FR-14: Quản lý Danh mục (Admin)
- Chỉ tài khoản có role=admin mới được truy cập trang quản lý danh mục; user thường hoặc chưa đăng nhập bị từ chối (403/redirect).
- Admin có thể xem danh sách, thêm mới, và xóa danh mục.
- Tên danh mục là bắt buộc nhập, không được để rỗng hoặc chỉ chứa khoảng trắng.
- Tên danh mục không được trùng với danh mục đã tồn tại.
- Hệ thống phải sanitize input để chống XSS/SQL injection khi thêm tên danh mục.
- Khi xóa danh mục đang có sản phẩm liên kết, hệ thống phải cảnh báo trước cho admin.
- API tạo/sửa/xóa danh mục ở backend phải kiểm tra role=admin, không chỉ chặn ở UI.

Observed behavior (những gì đã khám phá khi chạy app thực tế):
- Admin xem được đầy đủ danh sách danh mục
- User thường và chưa đăng nhập bị từ chối truy cập trang admin đúng như spec
- Thêm danh mục với tên rỗng vẫn thành công
- Thêm danh mục với tên chỉ có khoảng trắng vẫn thành công (không trim + validate)
- Thêm danh mục tên rất dài (255+ ký tự) vẫn thành công, không giới hạn độ dài
- Thêm danh mục trùng tên đã tồn tại vẫn thành công, không báo lỗi trùng
- Thêm tên chứa `<script>` hoặc SQL injection: hệ thống sanitize tốt, hiển thị dạng text bình thường, không execute
- Xóa danh mục đang có sản phẩm liên kết: vẫn xóa được, không có cảnh báo
- Dùng JWT token của user thường gọi trực tiếp API POST /api/categories: vẫn tạo được danh mục mới (backend không check role)

=== YÊU CẦU ===
Hãy thực hiện Domain Testing theo đúng 3 bước sau:

BƯỚC 1 — XÁC ĐỊNH BIẾN INPUT
- Liệt kê tất cả các biến input của feature (ví dụ: auth_role, category_name, category_id)
- Với mỗi biến: nêu tên, kiểu dữ liệu, mô tả

BƯỚC 2 — PHÂN VÙNG MIỀN (Domain Partitioning)
- Với mỗi biến, chia thành các miền: Valid / Invalid / Edge
- Mỗi miền cần có: tên miền, loại, điều kiện, ví dụ cụ thể
- Trình bày dưới dạng bảng Markdown

BƯỚC 3 — THIẾT KẾ TEST CASES
- Chọn ít nhất 1 test case đại diện cho mỗi miền
- Kết hợp các miền để tạo test scenario thực tế (vd: role=user + gọi API thêm danh mục trực tiếp)
- Trình bày dưới dạng bảng Markdown với các cột:
  TC ID | Mô tả | auth_role | category_name | category_id | Kết quả mong đợi | Kết quả thực tế | Pass/Fail
- Cột "Kết quả thực tế" và "Pass/Fail" để trống

BƯỚC 4 — BUG REPORTS
- Dựa trên observed behavior, xác định những điểm không khớp với spec
- Với mỗi bug: Bug ID, Severity, Priority, Summary, Steps, Expected, Actual

BƯỚC 5 — AI GAP ANALYSIS
- Liệt kê những test cases hoặc bugs mà AI có thể bỏ sót
- Giải thích lý do tại sao AI bỏ sót

=== OUTPUT FORMAT ===
Trả về file Markdown hoàn chỉnh theo cấu trúc:
# FR-14: Category Management (Admin) — Domain Testing Report
## 1. Feature Overview
## 2. Domain Testing — Step-by-step
### Bước 1: Xác định biến input
### Bước 2: Phân vùng miền
### Bước 3: Chọn test case đại diện
## 3. Test Case Table
## 4. Bug Reports
## 5. AI Gap Analysis
## 6. Test Summary

Prompt cho BVA:
Bạn là một QA Engineer chuyên nghiệp. Hãy áp dụng kỹ thuật Boundary Value Analysis (BVA) cho feature sau theo đúng các bước được mô tả.

=== THÔNG TIN FEATURE ===
Feature ID: FR-14
Feature Name: Category Management (Admin)
URL: http://localhost:5173/admin/categories
Spec:
### FR-14: Quản lý Danh mục (Admin)
- Tên danh mục bắt buộc nhập, không được rỗng hoặc chỉ chứa khoảng trắng.
- Tên danh mục có giới hạn độ dài tối đa (giả định VARCHAR(255) theo chuẩn DB phổ biến — cần confirm với BA nếu không có trong spec).
- Chỉ role=admin mới được tạo danh mục (kiểm tra ở cả UI và backend API).

Observed behavior:
- Tên rỗng (0 ký tự): không báo lỗi, thêm thành công với tên rỗng
- Tên 1-2 ký tự: thêm thành công bình thường
- Tên 255, 256, và 1000 ký tự: đều thêm thành công, không có giới hạn nào được áp dụng
- Tên chỉ có 1 hoặc nhiều khoảng trắng: hệ thống có trim nhưng không báo lỗi chuỗi rỗng sau khi trim
- Tên có khoảng trắng đầu/cuối kèm ký tự thường (vd " a", "a "): trim đúng và thêm thành công

Kết quả Domain Testing (nếu đã có): [paste kết quả Domain Testing FR-14 vào đây]

=== YÊU CẦU ===
Hãy thực hiện BVA theo đúng các bước sau:

BƯỚC 1 — XÁC ĐỊNH CÁC BIÊN (Boundaries)
- Liệt kê tất cả ràng buộc có giá trị biên rõ ràng (vd: độ dài tên danh mục)
- Với mỗi ràng buộc: nêu biến, điều kiện, nguồn (spec/observed/suy luận)

BƯỚC 2 — PHÂN TÍCH BIÊN TỪNG BIẾN
- Với mỗi biến có biên, vẽ trục số minh họa dạng:
  INVALID | VALID
  [giá trị] | [giá trị]
- Xác định các điểm biên:
  + Below lower bound
  + On-point lower bound (length=0) ← quan trọng nhất
  + Just above lower bound
  + Just below upper bound
  + On-point upper bound ← quan trọng nhất
  + Just above upper bound

BƯỚC 3 — THIẾT KẾ TEST CASES
- Mỗi điểm biên = 1 test case
- Trình bày dưới dạng bảng Markdown với các cột:
  TC ID | Mô tả | Input | Biên liên quan | Kết quả mong đợi | Kết quả thực tế | Pass/Fail
- Cột "Kết quả thực tế" và "Pass/Fail" để trống

BƯỚC 4 — BUG REPORTS
- Dựa trên observed behavior, xác định lỗi tại các điểm biên
- Đặc biệt chú ý: validation bị bỏ qua tại on-point (length=0, whitespace-only)

BƯỚC 5 — AI GAP ANALYSIS
- Những biên nào AI không thể tự test được (schema DB thực tế, quan hệ dữ liệu giữa bảng)
- Lý do cụ thể

=== OUTPUT FORMAT ===
Trả về file Markdown hoàn chỉnh theo cấu trúc:
# FR-14: Category Management (Admin) — BVA Report
## 1. Feature Overview
## 2. Xác định các biên
## 3. Phân tích biên từng biến
## 4. Test Case Table
## 5. Bug Reports
## 6. AI Gap Analysis
## 7. Test Summary
```

**AI Output (tóm tắt):**

```
Domain Testing bao phủ khá tốt các miền valid/invalid/edge của category_name (rỗng, khoảng trắng, dài, trùng, script/SQL injection) và auth_role (admin/user/chưa đăng nhập). Phát hiện 2 bug chính được viết báo cáo đầy đủ: tên rỗng vẫn thêm được, và role bypass qua API (user thường tạo được danh mục — SEC-03, mức Critical).
Domain Testing cũng phát hiện thêm 3 vấn đề fail trong bảng test case nhưng chưa được viết thành bug report (tên quá dài không giới hạn, tên trùng vẫn thêm được, xóa danh mục có sản phẩm không cảnh báo) — cần bổ sung thủ công.
BVA đào sâu vào biến category_name theo độ dài, xác nhận chính xác tại on-point length=0 là nơi validation bị bỏ qua, đồng thời mở rộng phát hiện thêm: không có giới hạn trên (255/256/1000 ký tự đều pass) và tên chỉ chứa khoảng trắng cũng không bị chặn dù có trim.
Sau khi gộp 2 báo cáo, loại trùng, và bổ sung bug report còn thiếu, kết quả cuối: 6 bug duy nhất, trong đó bug nghiêm trọng nhất vẫn là role bypass ở tầng API (Critical) — cho thấy BVA hiệu quả nhất ở việc khoanh vùng chính xác biên độ dài, còn Domain Testing hiệu quả hơn ở việc phát hiện các lỗi logic nghiệp vụ rộng (trùng tên, quan hệ dữ liệu, phân quyền).
```

---

### Interaction #06

| Field        | Detail                                        |
| ------------ | --------------------------------------------- |
| **Tool**     | Claude (claude.ai)                            |
| **Ngày giờ** | [2/7/2026 11:00]                              |
| **Mục đích** | Hỗ trợ test thực tế (DevTools, Console fetch) |

**Prompt:**

```
Giúp tôi viết đoạn code để dùng token user để gọi thử API tạo danh mục ngay trong Console phía Admin để kiểm tra security req SEC-03:API Admin phải kiểm tra `role = 'admin'` trong Token, không chỉ kiểm tra sự tồn tại của Token
```

**AI Output (tóm tắt):**

```
Bước 1: Đăng nhập tài khoản user thường → mở DevTools → Network → tìm request login → tab Response → copy JWT token
Bước 2: Paste vào Console:
javascriptconst userToken = "paste_token_user_thường_vào_đây";

fetch('http://localhost:3000/api/categories', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${userToken}`
  },
  body: JSON.stringify({
    name: "Test SEC-03"
  })
})
.then(r => r.json())
.then(data => console.log('Response:', data))
.catch(err => console.error('Error:', err))
Kết quả mong đợi: 403 Forbidden
```

---

### Interaction #07

| Field        | Detail                                        |
| ------------ | --------------------------------------------- |
| **Tool**     | Claude (claude.ai)                            |
| **Ngày giờ** | [2/7/2026 13:00]                              |
| **Mục đích** | Hỗ trợ test thực tế (DevTools, Console fetch) |

**Prompt:**

```

Giúp tôi viết đoạn code để gửi total_amount từ client lên xem thử backend có tính lại giá tiên thực tế chính xác hay chấp nhận bất kỳ giá trị nào
```

**AI Output (tóm tắt):**

```

const token = localStorage.getItem('token');

fetch('http://localhost:3000/api/checkout', {
method: 'POST',
headers: {
'Content-Type': 'application/json',
'Authorization': `Bearer ${token}`
},
body: JSON.stringify({
coupon_id: null,
items: [{ id: 1, name: "iPhone 15 Pro Max", price: 30000000 }],
total_amount: 1
})
})
.then(r => r.json())
.then(data => console.log('Response:', data))
.catch(err => console.error('Error:', err))

Kết quả quan sát Ý nghĩa
total_amount: 1 Backend chấp nhận giá trị client gửi (không validate)
total_amount: 30000000 Backend tự tính lại tổng tiền từ dữ liệu hợp lệ
401 Unauthorized Token sai → thử tìm kiếm token khác trong LocalStorage

```

### Interaction #08

| Field        | Detail                                        |
| ------------ | --------------------------------------------- |
| **Tool**     | Claude (claude.ai)                            |
| **Ngày giờ** | [2/7/2026 13:30]                              |
| **Mục đích** | Hỗ trợ test thực tế (DevTools, Console fetch) |

**Prompt:**

```
Giúp tôi viết đoạn code để gọi API cập nhật role từ phía client để kiểm tra secutiry req "SEC-06: API cập nhật hồ sơ không được cho phép thay đổi trường `role` từ client."
```

**AI Output (tóm tắt):**

```
Bước 1: Đăng nhập user thường → lấy token từ localStorage:
javascriptconst token = localStorage.getItem('token');
console.log(token);
Bước 2: Gọi API cập nhật profile với role=admin:
javascriptconst token = localStorage.getItem('token');

fetch('http://localhost:3000/api/users/me', {
  method: 'PUT',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${token}`
  },
  body: JSON.stringify({
    role: "admin"
  })
})
.then(r => r.json())
.then(data => console.log('Response:', data))
.catch(err => console.error('Error:', err))


Kết quả quan sát	Ý nghĩa	Pass/Fail
Response có role: "admin"	 Critical Bug! Backend trả role sai	Fail
Response bỏ qua trường role, role vẫn là "user"	 An toàn (không bị leo thang đặc quyền)	Pass
403 hoặc 400	 Bị chặn đúng cách	Pass
```

---

## Tổng kết sử dụng AI

| Task                          | AI hỗ trợ         | Human review                                  |
| ----------------------------- | ----------------- | --------------------------------------------- |
| Thiết kế Domain Testing FR-02 | AI tạo test cases | Review + bổ sung TC-19 (password plaintext)   |
| Thiết kế BVA FR-02            | AI tạo test cases | Review + phát hiện lockout 3 phút thay vì 30s |
| Thiết kế Domain Testing FR-08 | AI tạo test cases | Review + verify qua DevTools                  |
| Thiết kế BVA FR-08            | AI tạo test cases | Review                                        |
| Thiết kế Domain Testing FR-14 | AI tạo test cases | Review + test SQL injection, SEC-03           |
| Thiết kế BVA FR-14            | AI tạo test cases | Review + verify whitespace                    |
| Thiết kế Domain Testing FR-20 | AI tạo test cases | Review                                        |
| Thiết kế BVA FR-20            | AI tạo test cases | Review                                        |

| Phát hiện bug thực tế (DevTool, Console) | AI không chạy app | Human test thủ công |
| Viết bug report | AI hỗ trợ format | Human điền kết quả thực tế |

```

```
