# 🛒 Bài 6: Thực Hành End-to-End: Quy Trình O2C (Bán Hàng - Giao Hàng - Thu Tiền)

Bài này hướng dẫn thao tác toàn bộ chuỗi Order-to-Cash kinh điển. Từ góc độ nhân viên Sales (SD), Điều vận/Thủ kho (MM) và Kế toán doanh thu (FI).

---

## 💲 Bước 1: Khai Báo Giá Bán - Condition Record (T-Code: VK11)
Trước khi bán hàng, hệ thống phải biết giá của sản phẩm là bao nhiêu.
1. Vào T-Code **`VK11`**.
2. Nhập Condition Type: **`PR00`** (Giá bán chuẩn). Bấm Enter.
3. Chọn tổ hợp khóa: **Customer/Material** (Giá theo Khách hàng và Vật tư cụ thể).
4. Nhập: 
   * **Sales Org:** `SO01`, **Dist. Channel:** `10`
   * **Customer:** `C-001` (Khách hàng VIP)
   * **Material:** `LAPTOP-01`
   * **Amount:** `1000` (USD), **Per:** `1`, **UoM:** `PC` (1 Cái = 1000 USD).
5. Bấm **Save**. Giá đã được ghi nhận vào hệ thống.

---

## 📝 Bước 2: Tạo Đơn Hàng Bán - Sales Order (T-Code: VA01)
Khách hàng `C-001` gọi điện chốt mua 5 cái Laptop.
1. Vào T-Code **`VA01`**.
2. **Order Type:** `OR` (Standard Order). Bấm Enter.
3. **Sold-to Party:** Nhập `C-001`.
4. Nhập **PO Number** (Mã hợp đồng của khách nếu có).
5. Ở bảng vật tư bên dưới, nhập:
   * **Material:** `LAPTOP-01`
   * **Order Quantity:** `5`. Bấm Enter.
6. Hệ thống tự động:
   * Check tồn kho (ATP): Xác nhận có đủ hàng, hẹn ngày mai giao được.
   * Tính giá (Pricing): Móc dữ liệu từ VK11, tự nhân lên `5 x 1000 = 5000 USD`.
7. Bấm **Save**. Hệ thống sinh ra SO số: `20000001`. (Vẫn chưa có bút toán FI).

---

## 📦 Bước 3: Tạo Lệnh Giao Hàng - Outbound Delivery (T-Code: VL01N)
Đến ngày hẹn giao, nhân viên điều vận (Logistics) lệnh cho kho xuất hàng.
1. Vào T-Code **`VL01N`**.
2. **Shipping Point:** `SP01` (Điểm xuất hàng).
3. **Selection Date:** Ngày hôm nay.
4. **Order:** Điền số SO `20000001`. Bấm Enter.
5. Màn hình Delivery hiện ra, chuyển sang Tab **Picking** (Nhặt hàng).
6. Nhập ô **Picked Qty** (Số lượng nhặt thực tế): `5`. (Nếu kho chỉ tìm thấy 4 cái, nhập 4).
7. Bấm **Save**. Hệ thống sinh phiếu Delivery số `80000001`. Đưa phiếu này cho tài xế xe tải.

---

## 🚚 Bước 4: Xuất Hàng Chuyển Quyền Sở Hữu - PGI (T-Code: VL02N)
Xe tải chạy ra khỏi cổng nhà máy. Hàng đã rời đi. Thủ kho phải trừ tồn kho trên hệ thống.
1. Vào T-Code **`VL02N`**.
2. Nhập phiếu Delivery `80000001`.
3. Bấm nút **Post Goods Issue (PGI)** ở thanh công cụ phía trên.
4. Lệnh hoàn thành. Trạng thái phiếu đóng lại.
*(Bước này CỰC KỲ QUAN TRỌNG. SAP tự động gọi OBYC, sinh bút toán FI: Nợ Giá vốn hàng bán (COGS) / Có Hàng tồn kho vật tư. Báo cáo kho MB52 bị trừ đi 5 cái).*

---

## 🧾 Bước 5: Lập Hóa Đơn Bán Hàng - Billing (T-Code: VF01)
Kế toán bán hàng dựa vào phiếu xuất kho để lập Hóa đơn đòi tiền khách.
1. Vào T-Code **`VF01`**.
2. Ở ô Document, nhập số Delivery `80000001`. (SAP tự động lôi toàn bộ số tiền 5000$ và thuế lên).
3. Bấm **Save** (Hoặc bấm nút biểu tượng kính lúp để xem trước). Sinh ra Hóa đơn Billing số `90000001`.
*(Lúc này SAP tự động gọi VKOA, sinh bút toán doanh thu FI: Nợ Công nợ khách hàng (C-001) / Có Doanh Thu / Có Thuế VAT bán ra).*

---

## 💸 Bước 6: Nhận Tiền Thanh Toán Của Khách (T-Code: F-28)
Tuần sau, tiền nổi trong tài khoản ngân hàng. Kế toán công nợ xóa nợ cho khách.
1. Vào T-Code **`F-28`** (Post Incoming Payments).
2. Tương tự như F-53, điền tài khoản ngân hàng nhận tiền và số tiền `5500 USD` (Bao gồm VAT).
3. Ở ô **Account** dưới phần Open Item Selection, nhập mã khách hàng `C-001`.
4. Bấm **Process Open Items**. Đối trừ số tiền vừa nhận với dòng nợ đang mở (Màu đỏ).
5. Bấm **Save**. Khách hàng hết nợ. Chu trình O2C khép lại mỹ mãn!
