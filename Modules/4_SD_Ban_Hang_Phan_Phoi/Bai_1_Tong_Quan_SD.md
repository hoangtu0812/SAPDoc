# 🛒 Bài 1: Tổng Quan Phân Hệ SD (Sales and Distribution)

**Mục tiêu:** Nắm vững chu trình Bán hàng và Phân phối - nơi mang lại doanh thu cho doanh nghiệp.

---

## 📚 1. Phân hệ SD là gì?
Phân hệ **SAP SD (Sales and Distribution - Bán hàng và Phân phối)** quản lý toàn bộ các khâu liên quan đến việc bán sản phẩm/dịch vụ của công ty cho khách hàng.

Nó bao phủ quy trình từ lúc khách hàng hỏi giá, công ty báo giá, ký hợp đồng, nhận đơn hàng, đến khi đóng gói, vận chuyển xuất kho, và cuối cùng là xuất hóa đơn đòi tiền khách hàng.

## 🏢 2. Các quy trình cốt lõi (Chu trình O2C - Order to Cash)
Chu trình kinh điển của SD bao gồm các bước sau:
1. **Pre-sales (Trước bán hàng):**
   * Inquiry (Hỏi hàng): Khách hàng hỏi thông tin sản phẩm, giá cả.
   * Quotation (Báo giá): Công ty gửi báo giá chính thức có thời hạn cho khách hàng.
2. **Sales Order Processing (Xử lý Đơn hàng):**
   * Tạo Sales Order (Đơn hàng bán) dựa trên Báo giá hoặc tạo trực tiếp. Hệ thống sẽ kiểm tra xem hàng trong kho còn đủ không (ATP Check) và tính toán giá bán, chiết khấu, thuế (Pricing Procedure).
3. **Shipping (Giao hàng):**
   * Tạo Delivery Document (Phiếu giao hàng).
   * Picking & Packing (Lấy hàng và Đóng gói).
   * PGI - Post Goods Issue (Xuất kho giao hàng): Hàng chính thức rời khỏi kho công ty.
4. **Billing (Lên hóa đơn):**
   * Tạo Billing Document (Hóa đơn bán hàng) gửi cho khách.

## ⌨️ 3. Các Transaction Code (T-Code) Phổ Biến
* `VD01` / `XD01`: Tạo dữ liệu chủ Khách hàng (Customer Master).
* `VK11`: Khai báo giá bán, chiết khấu (Condition Records).
* `VA11`: Tạo Báo giá (Quotation).
* `VA01` / `VA02` / `VA03`: Tạo / Sửa / Xem Đơn hàng bán (Sales Order).
* `VL01N`: Tạo Phiếu giao hàng (Outbound Delivery).
* `VF01`: Tạo Hóa đơn bán hàng (Billing).
* `VA05`: Báo cáo danh sách Đơn hàng bán.

## 🔗 4. Tích hợp (Integration)
* **SD và MM:** Khi tạo Sales Order, SD "hỏi" MM xem kho còn hàng không. Khi làm PGI (Xuất kho), lượng tồn kho bên MM tự động giảm xuống.
* **SD và FI:** 
  * Khi PGI (Xuất kho), hệ thống sinh bút toán FI (Nợ Giá vốn hàng bán / Có Hàng tồn kho).
  * Khi làm `VF01` (Hóa đơn), hệ thống sinh bút toán FI ghi nhận Doanh thu và Tăng công nợ phải thu khách hàng (Accounts Receivable).
