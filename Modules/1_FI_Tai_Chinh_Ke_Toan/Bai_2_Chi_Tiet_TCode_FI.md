# 💰 Bài 2: Thuật Ngữ và Chi Tiết T-Code Phân Hệ FI

Dưới đây là phần đi sâu vào chi tiết các thuật ngữ cốt lõi, danh sách các T-Code (Transaction Code) quan trọng và cách chúng liên kết với nhau thành một luồng quy trình (Workflow) trong phân hệ Kế toán Tài chính (FI).

---

## 📚 1. Các Thuật Ngữ Cốt Lõi (Terminology)

* **Company Code (Mã công ty):** Đơn vị tổ chức nhỏ nhất có thể lập được Báo cáo tài chính (Bảng cân đối kế toán & Báo cáo kết quả kinh doanh) độc lập một cách hợp pháp.
* **Chart of Accounts (Hệ thống tài khoản):** Danh sách toàn bộ các tài khoản G/L được công ty sử dụng. (Ví dụ: Hệ thống tài khoản Việt Nam theo Thông tư 200).
* **G/L Account (General Ledger Account - Tài khoản sổ cái):** Tài khoản kế toán dùng để ghi nhận các nghiệp vụ kinh tế phát sinh (Ví dụ: TK 111 - Tiền mặt, TK 112 - Tiền gửi ngân hàng).
* **Document Type (Loại chứng từ):** Mã gồm 2 chữ cái dùng để phân loại chứng từ kế toán. Ví dụ: `SA` (Chứng từ G/L chung), `KR` (Hóa đơn nhà cung cấp), `DR` (Hóa đơn khách hàng), `KZ` (Thanh toán cho nhà cung cấp).
* **Posting Key (Mã hạch toán):** Mã số gồm 2 chữ số xác định dòng bút toán đó là Nợ (Debit) hay Có (Credit) và loại tài khoản đang ghi nhận (Khách hàng, Nhà cung cấp, hay G/L). Ví dụ: `40` = Nợ TK Sổ cái, `50` = Có TK Sổ cái, `31` = Có TK Nhà cung cấp (Tạo công nợ).

---

## ⌨️ 2. Chi Tiết Chức Năng Các T-Code Quan Trọng

### A. Quản lý Dữ liệu chủ (Master Data)
* **`FS00` (G/L Account Master):** Tạo, sửa, xem tài khoản Sổ cái. Nơi khai báo TK này là TK Bảng cân đối (Balance Sheet) hay TK Lỗ lãi (P&L).
* **`FK01` / `FK02` / `FK03` (Vendor Master - FI):** Quản lý hồ sơ nhà cung cấp (thông tin ngân hàng, điều khoản thanh toán) ở góc độ Kế toán. *(Ghi chú: Trên SAP S/4HANA dùng T-Code `BP`)*.
* **`FD01` / `FD02` / `FD03` (Customer Master - FI):** Quản lý hồ sơ khách hàng. *(Trên SAP S/4HANA dùng `BP`)*.

### B. Hạch toán Giao dịch (Transactions)
* **`FB50` (Enter G/L Account Document):** Hạch toán bút toán tổng hợp trực tiếp giữa các tài khoản Sổ cái (Ví dụ: Rút tiền gửi ngân hàng nhập quỹ tiền mặt).
* **`FB60` (Enter Vendor Invoice):** Kế toán ghi nhận hóa đơn dịch vụ phải trả cho Nhà cung cấp mà không đi qua quy trình mua hàng MM (Ví dụ: Hóa đơn tiền điện, tiền nước).
* **`FB70` (Enter Customer Invoice):** Kế toán ghi nhận hóa đơn dịch vụ xuất cho Khách hàng mà không đi qua quy trình bán hàng SD.
* **`F-53` (Post Outgoing Payments):** Chi tiền trả nhà cung cấp và cấn trừ (Clear) công nợ.
* **`F-28` (Post Incoming Payments):** Thu tiền từ khách hàng và cấn trừ công nợ.

### C. Báo cáo (Reporting)
* **`FBL3N` (G/L Account Line Items):** Xem sổ chi tiết tài khoản Sổ cái. Có màu Xanh (Đã Clear) và màu Đỏ (Đang mở/Chưa Clear).
* **`FBL1N` (Vendor Line Items):** Xem bảng kê công nợ phải trả theo từng nhà cung cấp.
* **`FBL5N` (Customer Line Items):** Xem bảng kê công nợ phải thu theo từng khách hàng.
* **`FAGLL03`:** Phiên bản Sổ cái mới (New GL) của FBL3N.

---

## 🔗 3. Sự Liên Quan Giữa Các T-Code (Quy Trình Ghi Sổ)

Để hiểu các T-Code hoạt động cùng nhau thế nào, hãy xem 2 chuỗi quy trình phổ biến sau:

### Quy trình 1: Chi phí văn phòng (Hạch toán G/L)
1. Kế toán trưởng dùng **`FS00`** để đảm bảo tài khoản Chi phí văn phòng và TK Tiền mặt đã tồn tại.
2. Nhân viên kế toán dùng **`FB50`** hạch toán: Nợ TK Chi phí văn phòng / Có TK Tiền mặt.
3. Cuối tháng, Kế toán tổng hợp dùng **`FBL3N`** xem báo cáo chi tiết TK Chi phí văn phòng để kiểm tra số dư và các dòng bút toán đã sinh ra.

### Quy trình 2: Nhận hóa đơn dịch vụ và thanh toán (A/P Workflow)
1. Kế toán tạo Nhà cung cấp (Vendor) trong hệ thống bằng **`FK01`** (hoặc `BP`).
2. Nhận hóa đơn tiền điện từ Vendor, Kế toán dùng **`FB60`** để ghi nhận nợ: Nợ TK Chi phí điện / Có TK Công nợ Vendor. Trạng thái chứng từ lúc này là Mở (Đỏ).
3. Kế toán dùng **`FBL1N`** mở sổ chi tiết Vendor lên, sẽ thấy khoản công nợ màu Đỏ đang chờ thanh toán.
4. Đến ngày hạn trả, Kế toán dùng **`F-53`** chi tiền thanh toán: Nợ TK Công nợ Vendor / Có TK Ngân hàng. Lệnh này sẽ **Clear** chứng từ mở ở bước 2.
5. Xem lại **`FBL1N`**, khoản nợ đã chuyển sang màu Xanh (Đã thanh toán).
