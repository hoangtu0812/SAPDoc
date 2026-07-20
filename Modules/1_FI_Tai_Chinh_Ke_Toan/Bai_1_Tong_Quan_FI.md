# 💰 Bài 1: Tổng Quan Phân Hệ FI (Financial Accounting)

**Mục tiêu:** Hiểu rõ vai trò của phân hệ Kế toán Tài chính (FI) trong SAP ERP và các quy trình nghiệp vụ cơ bản.

---

## 📚 1. Phân hệ FI là gì?
Phân hệ **SAP FI (Financial Accounting)** là một trong những phân hệ quan trọng và cốt lõi nhất của hệ thống SAP. Chức năng chính của nó là theo dõi, ghi nhận và báo cáo các luồng dữ liệu tài chính của doanh nghiệp ra bên ngoài (cho cơ quan thuế, ngân hàng, cổ đông, v.v.).

Mục đích chính của SAP FI là tạo ra các **Báo cáo tài chính (Financial Statements)** như Bảng cân đối kế toán (Balance Sheet) và Báo cáo kết quả hoạt động kinh doanh (Profit and Loss Statement - P&L).

## 🏢 2. Các thành phần chính (Sub-modules)
Phân hệ FI được chia thành nhiều mảng nhỏ xử lý các nghiệp vụ chuyên sâu:
* **G/L (General Ledger - Sổ cái chung):** Ghi nhận toàn bộ các bút toán kế toán. Đây là trung tâm của hệ thống kế toán.
* **A/R (Accounts Receivable - Kế toán công nợ phải thu):** Quản lý các giao dịch với khách hàng (liên kết chặt chẽ với phân hệ SD).
* **A/P (Accounts Payable - Kế toán công nợ phải trả):** Quản lý các giao dịch với nhà cung cấp (liên kết chặt chẽ với phân hệ MM).
* **AA (Asset Accounting - Kế toán tài sản cố định):** Quản lý toàn bộ vòng đời của tài sản (mua sắm, khấu hao, thanh lý).
* **BL (Bank Ledger - Kế toán ngân hàng):** Quản lý các giao dịch với ngân hàng, đối chiếu sổ phụ ngân hàng (Bank Reconciliation).

## ⌨️ 3. Các Transaction Code (T-Code) Phổ Biến
Dưới đây là một số T-Code mà người dùng FI hay sử dụng hàng ngày:
* `FB50`: Hạch toán bút toán Sổ cái (G/L Account Posting).
* `FB70`: Hạch toán hóa đơn phải thu khách hàng (Customer Invoice).
* `FB60`: Hạch toán hóa đơn phải trả nhà cung cấp (Vendor Invoice).
* `F-28`: Ghi nhận tiền khách hàng thanh toán (Incoming Payment).
* `F-53`: Chi tiền trả nhà cung cấp (Outgoing Payment).
* `FBL3N`: Xem báo cáo chi tiết các tài khoản Sổ cái (G/L Account Line Items).
* `AW01N`: Xem công cụ khám phá tài sản (Asset Explorer).

## 🔗 4. Tích hợp (Integration)
SAP FI không hoạt động độc lập mà tích hợp chặt chẽ với các phân hệ khác:
- **FI - MM (Procure to Pay):** Khi nhập kho hàng hóa từ nhà cung cấp, hệ thống tự động sinh bút toán kế toán ghi tăng tài sản/hàng hóa và tăng công nợ phải trả.
- **FI - SD (Order to Cash):** Khi xuất hóa đơn bán hàng cho khách, hệ thống tự động ghi nhận doanh thu và tăng công nợ phải thu.
- **FI - CO:** Mọi chi phí và doanh thu từ FI đều được đẩy sang CO để phân tích hiệu quả quản trị.
