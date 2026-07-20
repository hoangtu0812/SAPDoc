# 🔧 Bài 1: Tổng Quan Phân Hệ PM (Plant Maintenance)

**Mục tiêu:** Nắm bắt quy trình Bảo trì Bảo dưỡng nhà máy - Phân hệ cốt lõi trong các ngành công nghiệp sản xuất nặng, dầu khí, hóa chất.

---

## 📚 1. Phân hệ PM là gì?
Phân hệ **SAP PM (Plant Maintenance - Bảo trì Nhà máy)** giúp doanh nghiệp quản lý tình trạng hoạt động của toàn bộ máy móc, thiết bị, dây chuyền sản xuất. 
Mục tiêu là đảm bảo máy móc hoạt động liên tục, giảm thiểu thời gian "chết" (Downtime), và kéo dài tuổi thọ thiết bị.

## 🏢 2. Các quy trình cốt lõi
* **Breakdown Maintenance (Bảo trì khắc phục/Sửa chữa đột xuất):** Máy đột ngột hư, nhân viên vận hành báo lỗi, đội kỹ thuật tới sửa.
* **Preventive Maintenance (Bảo trì phòng ngừa):** Lên lịch bảo dưỡng định kỳ (ví dụ: cứ chạy 10,000 giờ hoặc sau 3 tháng thì phải thay nhớt máy bơm).
* **Predictive Maintenance (Bảo trì dự đoán):** Ứng dụng IoT, đo độ rung, nhiệt độ để dự đoán thời điểm máy sắp hỏng và sửa trước.
* **Refurbishment (Đại tu):** Sửa chữa và phục hồi các thiết bị giá trị cao (như mô-tơ bơm) từ trạng thái hỏng thành trạng thái hoạt động tốt để cất lại vào kho.

## 📚 3. Các Thuật Ngữ Cốt Lõi (Terminology)
* **Functional Location (Vị trí chức năng - FL):** Nơi lắp đặt thiết bị. Được tổ chức theo hình cây. (VD: Nhà máy A -> Phân xưởng 1 -> Bơm nước).
* **Equipment (Thiết bị):** Đối tượng vật lý cụ thể, có số Serial (VD: Máy bơm nước số hiệu B-001). Một thiết bị (Equipment) sẽ được lắp vào một vị trí (FL).
* **BOM (Bill of Material) Thiết bị:** Danh sách các phụ tùng (Spare parts) cấu thành nên máy đó.
* **Notification (Thông báo):** Lời nhắn báo lỗi hoặc yêu cầu dịch vụ (VD: Máy bơm B-001 bị rỉ nước).
* **Maintenance Order (Đơn hàng bảo trì):** Phiếu giao việc chính thức cho thợ sửa chữa, trong đó ghi rõ cần bao nhiêu giờ công, cần xuất kho vật tư gì để sửa.

## 🔗 4. Tích hợp (Integration)
* **PM và MM:** Để sửa máy, thợ cần xuất kho phụ tùng (Ví dụ: Vòng bi). PM Order sẽ tự động tạo Reservation (Giữ chỗ vật tư) bên kho MM. Nếu kho hết, PM Order tự động sinh PR gửi đi mua hàng.
* **PM và CO:** Toàn bộ chi phí vật tư xuất ra sửa máy, cộng với chi phí tiền công (Labor cost) của thợ sửa, sẽ được gom vào PM Order và cuối tháng "cấn trừ" (Settle) sang Cost Center của phân xưởng sử dụng máy đó.
