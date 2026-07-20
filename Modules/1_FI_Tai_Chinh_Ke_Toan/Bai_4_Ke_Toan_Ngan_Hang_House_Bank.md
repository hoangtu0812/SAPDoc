# 🏦 Bài 4: Chuyên Sâu Về Kế Toán Ngân Hàng (House Bank & Bank Account)

Trong phân hệ FI, việc quản lý dòng tiền qua Ngân hàng (FI-BL: Bank Ledger) là cực kỳ quan trọng. 

Dưới đây là hướng dẫn chi tiết cách cấu hình một Tài khoản ngân hàng công ty (House Bank) và tại sao quy trình này ở bản SAP S/4HANA lại khác biệt hoàn toàn so với bản SAP ECC cũ.

---

## 1. Thuật Ngữ Cốt Lõi
* **Bank Master Data:** Danh mục các ngân hàng trên toàn cầu (Ví dụ: Vietcombank, BIDV) kèm theo mã Swift Code. T-Code: `FI01`.
* **House Bank (Ngân hàng nhà):** Đại diện cho một chi nhánh ngân hàng cụ thể mà công ty bạn mở tài khoản (Ví dụ: Vietcombank CN Tân Bình). Mã gồm 5 ký tự.
* **Account ID (Mã tài khoản):** Đại diện cho một số tài khoản thực tế tại House Bank đó (Ví dụ: TK VNĐ 001100..., TK USD 001100...). Mã gồm 5 ký tự.
* **G/L Account (Tài khoản Sổ cái):** Mỗi Account ID bắt buộc phải map (ánh xạ) với 1 tài khoản G/L bên FI (Thường là đuôi 112* theo chuẩn VN).

---

## 2. Cấu Hình House Bank Trên SAP ECC (Bản cũ)

Ở bản SAP cũ, mọi thứ được làm chung trong một màn hình duy nhất.

1. Vào T-Code **`FI12`**.
2. Nhập Mã Công ty (Ví dụ: `VN01`).
3. Tạo mới một **House Bank** (Mã: `VCBTB`, Tên: Vietcombank Tan Binh, nhập mã số ngân hàng Bank Key).
4. Nhấn đúp vào mục **Bank Accounts** ở cột bên trái.
5. Tạo mới một **Account ID** (Mã: `VND01`).
6. Nhập Số tài khoản ngân hàng thực tế (Bank Account Number: `001100123456`).
7. **Mapping:** Kéo xuống dưới, nhập tài khoản Sổ cái G/L (Ví dụ: `112101`) vào ô G/L Account.
8. Lưu lại. Bạn đã có thể dùng mã `VCBTB` / `VND01` để làm lệnh thanh toán (F-53).

---

## 3. Quản Lý Tài Khoản Ngân Hàng Trên SAP S/4HANA (Bản mới)

SAP S/4HANA tách biệt quy trình này ra để kiểm soát bảo mật tốt hơn (Tránh việc nhân viên kế toán tự ý thêm số tài khoản ngân hàng ảo để rút tiền).

Tính năng này gọi là **Bank Account Management (BAM)** thuộc gói **Cash Management**.

### Bước 1: Tạo House Bank (Bằng T-Code)
1. Bạn vẫn vào T-Code **`FI12`** (hoặc `FI12_HBANK`).
2. Tuy nhiên, bạn **CHỈ** được phép tạo House Bank (Mã `VCBTB` và Bank Key). Mục Bank Accounts ở cột bên trái đã bị khóa mờ (Disabled). Bạn không thể nhập số tài khoản ở đây nữa!

### Bước 2: Tạo Account ID và Số Tài Khoản (Bằng Fiori App / NWBC)
1. Bạn phải mở trình duyệt web lên, đăng nhập vào **Fiori Launchpad** (hoặc dùng T-Code `NWBC`).
2. Mở ứng dụng **Manage Bank Accounts** (Quản lý tài khoản ngân hàng).
3. Tại đây, bạn bấm tạo mới, nhập Số tài khoản ngân hàng (`001100123456`).
4. Gắn nó vào House Bank `VCBTB` đã tạo ở Bước 1.
5. Sinh ra Account ID `VND01` và map với G/L Account `112101`.
6. Hệ thống sẽ sinh ra một Workflow duyệt (Approval). Kế toán trưởng phải vào duyệt thì số tài khoản này mới "Active" và được phép dùng để hạch toán trong hệ thống.

---

## 4. EBS (Electronic Bank Statement) - Sao kê điện tử
Thay vì kế toán phải lấy sổ phụ ngân hàng (bản cứng) rồi hạch toán tay từng giao dịch thu/chi, SAP có tính năng **EBS**.
* Ngân hàng xuất ra một file sao kê định dạng chuẩn quốc tế (MT940 hoặc XML).
* Kế toán dùng T-Code **`FF_5`** để import file này vào SAP.
* SAP tự động đọc file, tìm mã khách hàng để cấn trừ công nợ, tìm mã Vendor để đối trừ thanh toán. Tiết kiệm hàng giờ nhập liệu mỗi ngày!
