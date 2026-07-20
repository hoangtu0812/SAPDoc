# 📦 Bài 1: Tổng Quan Phân Hệ MM (Materials Management)

**Mục tiêu:** Hiểu quy trình Mua sắm và Quản lý Kho - xương sống của chuỗi cung ứng trong SAP.

---

## 📚 1. Phân hệ MM là gì?
Phân hệ **SAP MM (Materials Management - Quản lý Vật tư)** đóng vai trò chủ đạo trong việc đảm bảo doanh nghiệp luôn có đủ vật tư, hàng hóa để sản xuất kinh doanh với mức chi phí tối ưu nhất.

MM đảm nhận toàn bộ quy trình từ khâu lập kế hoạch nhu cầu vật tư, đi mua hàng (Purchasing), quản lý nhập xuất tồn kho (Inventory Management), cho đến khâu nhận hóa đơn của nhà cung cấp (Invoice Verification).

## 🏢 2. Các quy trình cốt lõi
* **Master Data (Dữ liệu chủ):** Quản lý Thông tin Vật tư (Material Master), Thông tin Nhà cung cấp (Vendor Master) và Bảng giá mua (Purchasing Info Record).
* **Purchasing (Mua sắm):** Toàn bộ chu trình mua hàng, bao gồm:
  1. Yêu cầu mua hàng (Purchase Requisition - PR).
  2. Lựa chọn nhà cung cấp và Yêu cầu báo giá (RFQ).
  3. Lên Đơn đặt hàng (Purchase Order - PO).
* **Inventory Management (Quản lý kho):** Quản lý luồng dịch chuyển vật lý của hàng hóa:
  * Nhập kho (Goods Receipt).
  * Xuất kho (Goods Issue).
  * Điều chuyển kho (Transfer Posting).
* **Physical Inventory (Kiểm kê kho):** Đối chiếu số lượng thực tế trong kho với số lượng trên hệ thống.
* **LIV (Logistic Invoice Verification - Xác minh hóa đơn):** Kiểm tra và đối chiếu hóa đơn nhà cung cấp gửi đến (Khớp 3 bên: Đơn đặt hàng - Phiếu nhập kho - Hóa đơn).

## ⌨️ 3. Các Transaction Code (T-Code) Phổ Biến
* `MM01` / `MM02` / `MM03`: Tạo / Sửa / Xem Dữ liệu chủ Vật tư (Material Master).
* `ME51N`: Tạo Yêu cầu mua hàng (Purchase Requisition).
* `ME21N`: Tạo Đơn đặt hàng (Purchase Order).
* `MIGO`: Thực hiện các giao dịch kho (Nhập/Xuất/Điều chuyển).
* `MIRO`: Ghi nhận hóa đơn nhà cung cấp (Invoice Verification).
* `MB52`: Xem báo cáo Tồn kho hiện tại.

## 🔗 4. Tích hợp (Integration)
* **Quy trình P2P (Procure to Pay):** MM kết hợp chặt chẽ với FI. Khi kho làm thao tác `MIGO` (Nhập kho), hệ thống tự động sinh bút toán FI (Nợ Hàng tồn kho / Có Tài khoản chờ GR/IR). Khi làm `MIRO` (Xác minh hóa đơn), hệ thống sinh bút toán FI ghi nhận Công nợ phải trả Vendor (Nợ GR/IR / Có Phải trả người bán).
* **MM và SD:** MM đảm bảo có đủ hàng trong kho để SD tiến hành xuất kho giao cho khách hàng.
