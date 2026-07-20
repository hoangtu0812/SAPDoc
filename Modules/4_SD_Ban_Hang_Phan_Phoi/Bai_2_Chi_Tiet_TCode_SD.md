# 🛒 Bài 2: Thuật Ngữ và Chi Tiết T-Code Phân Hệ SD

Trong phân hệ Bán hàng & Phân phối (SD), mọi thứ xoay quanh việc lấy đơn hàng và giao đúng hẹn cho khách, đồng thời mang tiền về cho công ty.

---

## 📚 1. Các Thuật Ngữ Cốt Lõi (Terminology)

* **Sales Organization (Tổ chức bán hàng):** Đơn vị pháp lý chịu trách nhiệm bán sản phẩm và các quyền lợi của khách hàng (Ví dụ: Công ty TNHH Bán Lẻ A).
* **Distribution Channel (Kênh phân phối):** Cách thức sản phẩm đến tay người dùng (Ví dụ: Bán sỉ, Bán lẻ, Bán Online).
* **Division (Ngành hàng):** Dùng để phân nhóm các dòng sản phẩm (Ví dụ: Đồ điện tử, Điện lạnh).
* **Sales Area (Khu vực bán hàng):** Là tổ hợp của 3 yếu tố trên (Sales Org + Dist. Channel + Division). Mọi giao dịch SD đều bắt buộc phải nhập Sales Area.
* **Pricing Procedure (Thủ tục tính giá):** Một công thức toán học vô cùng linh hoạt của SAP để tính từ Giá gốc (Base Price) -> Trừ Chiết khấu (Discount) -> Cộng Thuế (Tax) -> Ra Giá cuối cùng (Net Value).
* **Condition Type (Loại điều kiện):** Các thành phần cấu thành nên Pricing Procedure. Ví dụ: `PR00` (Giá gốc), `K004` (Chiết khấu số lượng), `MWST` (Thuế VAT).
* **ATP (Available To Promise):** Tính năng kiểm tra tồn kho cực hay của SD. Khi gõ số lượng khách mua vào Đơn hàng, hệ thống tính toán ngay lập tức kho còn bao nhiêu, hàng đang đi đường sắp về bao nhiêu, và chốt ngày có thể giao hàng cho khách.

---

## ⌨️ 2. Chi Tiết Chức Năng Các T-Code Quan Trọng

### A. Quản lý Dữ liệu chủ (Master Data)
* **`VD01` / `VD02` / `VD03` (Customer Master):** Tạo hồ sơ Khách hàng phần SD (Nơi khai báo Kênh phân phối, Điều kiện giao hàng...). *(Từ S/4HANA gộp thành T-code `BP`)*.
* **`VK11` / `VK12` / `VK13` (Condition Records):** Cài đặt giá bán và chiết khấu. Ví dụ: Cài đặt cho Vật tư A, nếu khách mua 100 cái thì giá là 5$, mua 500 cái giá giảm còn 4$.

### B. Chuỗi Bán hàng (Order Processing)
* **`VA11` (Create Quotation):** Tạo bảng báo giá chính thức gửi khách hàng.
* **`VA01` / `VA02` / `VA03` (Sales Order):** Tạo Đơn hàng bán. Có thể tạo độc lập hoặc tham chiếu (copy) dữ liệu từ Quotation (Báo giá).
* **`VL01N` (Create Outbound Delivery):** Nhân viên điều vận tạo Phiếu xuất hàng, báo cho thủ kho biết sắp có xe tới lấy hàng.
* **`VL02N` (Change Delivery - PGI):** Sau khi hàng đã lên xe tải rời đi, thủ kho/điều vận vào bấm nút **PGI (Post Goods Issue)** để chính thức cấn trừ tồn kho.
* **`VF01` (Create Billing Document):** Kế toán hoặc Sale Admin tạo Hóa đơn bán hàng để gửi cho khách yêu cầu thanh toán.

### C. Báo cáo (Reporting)
* **`VA05` (List of Sales Orders):** Xem danh sách tất cả các Đơn hàng trong một khoảng thời gian, có thể lọc theo trạng thái Mở/Đóng.
* **`VF05` (List of Billing Documents):** Xem danh sách Hóa đơn bán hàng đã xuất.

---

## 🔗 3. Sự Liên Quan Giữa Các T-Code (Chu Trình O2C - Order to Cash)

Luồng nghiệp vụ bán hàng chuẩn (O2C) vận hành qua các bước sau:

1. **Báo giá (`VA11`):** Sale tạo báo giá (Quotation) mã số #100 gửi khách.
2. **Đơn hàng (`VA01`):** Khách đồng ý mua, Sale vào `VA01` nhập lệnh tạo Đơn hàng tham chiếu tới Báo giá #100.
   - Hệ thống tự check ATP: Kho báo đủ hàng.
   - Hệ thống tự check giá (`VK11`): Kéo giá bán và chiết khấu lên màn hình. Đơn hàng #200 được lưu. Chưa có bút toán kế toán.
3. **Giao hàng (`VL01N` & `VL02N`):** 
   - Đến ngày giao, kho tạo phiếu Giao hàng (`VL01N`).
   - Đóng gói xong, xe tải chạy đi, kho ấn nút **PGI** trong `VL02N`.
   - *Hệ thống ngầm định:* Giảm tồn kho bên MM (báo cáo `MB52` bị tụt). Sinh bút toán kế toán FI (Nợ Giá vốn hàng bán / Có Hàng tồn kho).
4. **Hóa đơn (`VF01`):** 
   - Nhân viên chứng từ xuất hóa đơn (`VF01`) dựa trên phiếu Giao hàng.
   - *Hệ thống ngầm định:* Sinh bút toán FI doanh thu (Nợ Công nợ khách hàng / Có Doanh thu / Có Thuế VAT).
5. **Thu tiền (`F-28` - của FI):** Khách hàng chuyển khoản, kế toán FI vào `F-28` ghi nhận tiền vào và triệt tiêu công nợ. Chu trình O2C khép lại.
