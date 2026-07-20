# 🛠️ Bài 2: Chi Tiết T-Code Phân Hệ PM

Bài này đi sâu vào các công cụ T-Code thao tác hàng ngày của kỹ sư bảo trì và luồng quy trình sửa chữa (Breakdown Maintenance) trong SAP PM.

---

## ⌨️ 1. Chi Tiết Các T-Code Quan Trọng

### A. Quản lý Dữ liệu chủ (Master Data)
* **`IL01` / `IL02` / `IL03` (Functional Location):** Khởi tạo sơ đồ vị trí lắp đặt máy. (Ví dụ tạo một nhánh: `Nhà máy A -> Dây chuyền 1 -> Bơm ly tâm`).
* **`IE01` / `IE02` / `IE03` (Equipment):** Tạo hồ sơ thiết bị (Ví dụ: Bơm số P-001). Ghi nhận các thông số như Nhà sản xuất, Model, Năm sản xuất, Trọng lượng.
* **`IB01` / `IB02` / `IB03` (Equipment BOM):** Tạo danh mục phụ tùng của thiết bị (VD: Bơm P-001 gồm 2 vòng bi, 1 trục, 1 cánh quạt). Giúp kỹ sư chọn nhanh phụ tùng khi máy hư.
* **`IP41` / `IP42` / `IP43` (Maintenance Plan):** Cài đặt kế hoạch bảo trì phòng ngừa (Ví dụ: Lịch bơm mỡ bôi trơn 1 tháng/lần).

### B. Chuỗi Giao dịch Sửa chữa (Transactions)
* **`IW21` / `IW22` / `IW23` (Create Notification):** Tạo phiếu Báo hỏng (Khai báo máy nào hỏng, hỏng bộ phận nào, mức độ khẩn cấp).
* **`IW31` / `IW32` / `IW33` (Create Order):** Tạo Đơn hàng bảo trì (Work Order) để lên kế hoạch sửa chữa: Cần thợ bậc bậc nào, mất bao lâu, xuất kho món đồ gì.
* **`IW41` (Time Confirmation):** Thợ sau khi sửa xong vào đây báo cáo "Tôi đã làm mất 4 tiếng". (Bước này để ghi nhận chi phí nhân công).
* **`MIGO` (Goods Issue):** Xuất kho vật tư mang đi sửa máy.

### C. Báo cáo (Reporting)
* **`IW28` / `IW29`:** Danh sách các Notification (Xem máy nào hỏng nhiều nhất).
* **`IW38` / `IW39`:** Danh sách các PM Order (Theo dõi tiến độ sửa chữa của thợ).
* **`IH08`:** Báo cáo danh sách thiết bị.

---

## 🔗 2. Sự Liên Quan Giữa Các T-Code (Quy Trình Breakdown)

Hãy xem cách các T-Code kết nối trong trường hợp máy bơm bị nghẹt:

1. **Báo lỗi (`IW21`):** Công nhân đứng máy thấy Bơm P-001 rung lắc và kêu to, dùng `IW21` tạo một Notification. Cảnh báo gửi tới phòng Kỹ thuật.
2. **Lập lệnh sửa (`IW31`):** Tổ trưởng Kỹ thuật mở Notification, xác nhận cần thay Vòng bi. Dùng `IW31` chuyển Notification này thành PM Order.
   - Trong Order, Tổ trưởng chọn vật tư "Vòng bi mã V-01" từ BOM (`IB01`), nhập số lượng 1 cái.
   - Giao việc cho Thợ cơ khí (Mất 2 giờ công).
   - Hệ thống PM Order gửi lệnh sang kho (MM) giữ chỗ 1 vòng bi.
3. **Thực thi (Thợ sửa):**
   - Thợ qua kho nhận vòng bi. Thủ kho dùng **`MIGO`** (Movement type 261) xuất kho Vòng bi cho PM Order. Sinh bút toán FI: Nợ Chi phí sửa chữa / Có Hàng tồn kho.
   - Thợ tháo máy, thay vòng bi xong.
4. **Nghiệm thu & Báo công (`IW41`):** Thợ vào `IW41` xác nhận đã làm 2 tiếng. Sinh bút toán CO tính tiền công 2 tiếng vào PM Order.
5. **Đóng lệnh (TECO - Technical Complete):** Tổ trưởng kiểm tra máy chạy tốt, vào `IW32` bấm nút "TECO" để đóng lệnh.
6. **Cấn trừ chi phí (Settlement - `KO88`):** Cuối tháng, Kế toán dùng `KO88` dọn dẹp chi phí trong PM Order đẩy về Cost Center của Xưởng.
