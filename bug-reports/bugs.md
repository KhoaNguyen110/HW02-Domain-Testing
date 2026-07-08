# Bugs Summary (FR-02, FR-08, FR-14, FR-20)

## Unique Bugs

| ID      | Feature                   | Severity | Bug Summary                                                                  | Nguon phat hien |
| ------- | ------------------------- | -------- | ---------------------------------------------------------------------------- | --------------- |
| FR02-01 | FR-02 Login               | Minor    | Tieu de trang login hien thi sai ("Dang ki" thay vi "Dang nhap")             | Domain          |
| FR02-02 | FR-02 Login               | Major    | Tai khoan bi lockout nhung khong hien thi thong bao/countdown cho nguoi dung | Domain, BVA     |
| FR02-03 | FR-02 Login               | Major    | Mat khau khong duoc an khi nhap                                              | Domain          |
| FR02-04 | FR-02 Login               | Major    | Lockout kich hoat som o lan sai thu 2 (spec yeu cau tu lan thu 3)            | BVA             |
| FR02-05 | FR-02 Login               | Major    | Thoi gian lockout thuc te ~3 phut thay vi 30 giay theo spec                  | Domain, BVA     |
| FR02-06 | FR-02 Login               | Major    | Email sai format khong bi chan o client (chi bao "Dang nhap that bai")       | Domain, BVA     |
| FR02-07 | FR-02 Login               | Major    | Email viet hoa toan bo khong dang nhap duoc (khong case-insensitive)         | Domain          |
| FR02-08 | FR-02 Login               | Critical | User thuong co the tu nang role thanh admin qua API cap nhat profile         | Domain          |
| FR08-01 | FR-08 Checkout            | Critical | Tong tien checkout cho phep chinh sua truc tiep tren UI (khong readonly)     | Domain, BVA     |
| FR08-02 | FR-08 Checkout            | Critical | Backend chap nhan total_amount do client sua (0/am/nho hon/gioi han khac)    | Domain, BVA     |
| FR08-03 | FR-08 Checkout            | Major    | Checkout thanh cong nhung gio hang khong bi xoa                              | Domain          |
| FR08-04 | FR-08 Checkout            | Major    | Van cho checkout khi thieu dia chi/so dien thoai nhan hang                   | Domain          |
| FR14-01 | FR-14 Category Management | Major    | Cho phep them danh muc voi ten rong                                          | Domain, BVA     |
| FR14-02 | FR-14 Category Management | Minor    | Cho phep them danh muc chi gom khoang trang                                  | Domain, BVA     |
| FR14-03 | FR-14 Category Management | Minor    | Khong gioi han do dai ten danh muc (1000+ ky tu van them duoc)               | BVA             |
| FR14-04 | FR-14 Category Management | Major    | Cho phep them danh muc trung ten                                             | Domain          |
| FR14-05 | FR-14 Category Management | Major    | Co the xoa danh muc dang co san pham lien ket                                | Domain          |
| FR14-06 | FR-14 Category Management | Critical | User thuong dung token van goi duoc API tao danh muc (RBAC bypass)           | Domain          |
| FR20-01 | FR-20 Mobile Login        | Minor    | Label email hien thi "Username" thay vi "Email" va keyboard khong dung kieu  | Domain          |
| FR20-02 | FR-20 Mobile Login        | Major    | Email/password rong khong duoc validate client-side                          | Domain, BVA     |
| FR20-03 | FR-20 Mobile Login        | Minor    | Email sai format khong duoc validate client-side                             | Domain, BVA     |
| FR20-04 | FR-20 Mobile Login        | Major    | Lockout kich hoat sau 2 lan sai thay vi 3 lan                                | Domain, BVA     |
| FR20-05 | FR-20 Mobile Login        | Major    | Silent lockout, khong hien thi thong bao bi khoa rieng                       | Domain, BVA     |
| FR20-06 | FR-20 Mobile Login        | Major    | Thoi gian lockout thuc te ~3 phut thay vi 30 giay                            | Domain, BVA     |
| FR20-07 | FR-20 Mobile Login        | Minor    | Email viet hoa khong dang nhap duoc                                          | Domain          |

## Screenshot Location

- bug-reports/screenshots/
- reports/FR-02_Login/screenshots/
- reports/FR-08_Checkout/screenshots/
- reports/FR-14_CategoryManagement/screenshots/
- reports/FR20_Mobile_Login/screenshots/

## Test Summary

| Metric            | Value                   |
| ----------------- | ----------------------- |
| Total test cases  | 114 (35 + 25 + 27 + 27) |
| Executed          | 113                     |
| Passed            | 53                      |
| Failed            | 60                      |
| Not executed      | 1                       |
| Unique bugs found | 25                      |
