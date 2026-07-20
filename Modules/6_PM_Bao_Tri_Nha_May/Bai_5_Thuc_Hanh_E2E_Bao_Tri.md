# 🔧 Bài 5: Thực Hành End-to-End: Bảo Trì Sự Cố Máy Móc (PM Breakdown)

Đóng vai trò là một kỹ sư bảo trì, bạn sẽ thực hiện quy trình từ lúc tiếp nhận thông báo lỗi của công nhân, đến lúc rút phụ tùng đi sửa và chốt lệnh.

---

## 🚨 Bước 1: Tiếp Nhận Báo Lỗi Máy (T-Code: IW21)
Công nhân đang vận hành Máy Bơm Nước (Mã số P-001) thì thấy có tiếng kêu to, rỉ nhớt. Họ lập tức tạo Báo hỏng (Notification) gửi về tổ kỹ thuật.
1. Vào T-Code **`IW21`** (Create Notification).
2. **Notification Type:** Chọn mã `M1` (Breakdown/Sự cố đột xuất). Bấm Enter.
3. Ở khung mô tả (Description), gõ: "Máy bơm P-001 kêu to và rỉ nhớt".
4. Ở ô **Equipment**, điền mã máy: `P-001`. (Hệ thống tự động lôi vị trí nhà xưởng của máy này lên).
5. Ở bảng Damage (Lỗi), có thể chọn mã lỗi chuẩn (VD: Lỗi Cơ khí - Hỏng vòng bi).
6. Bấm **Save**. Thông báo số `10000001` được gửi thẳng lên màn hình của Tổ trưởng kỹ thuật.

---

## 🛠️ Bước 2: Tạo Đơn Hàng Sửa Chữa (T-Code: IW31)
Tổ trưởng kiểm tra tình hình, xác định phải thay cụm Vòng Bi.
1. Vào T-Code **`IW31`** (Create Order).
2. **Order Type:** `PM01` (Sửa chữa sự cố). Ở ô Notification, điền mã `10000001` vừa tạo ở Bước 1 vào để link chúng lại với nhau. Bấm Enter.
3. Ở Tab **Operations (Thao tác):**
   * Nhập hướng dẫn cho thợ: "Tháo lốc máy, thay vòng bi số 2".
   * Giao việc: Work Center (Nhóm thợ Cơ khí).
   * Ước tính giờ làm (Planned Work): `2` (Giờ).
4. Ở Tab **Components (Phụ tùng):**
   * Nhập mã vật tư: `V-001` (Vòng bi). Số lượng: `1`.
   * Item Category: `L` (Hàng lấy từ kho - Stock item).
5. Nhấn nút **Release** (Cờ xanh lá) trên thanh công cụ để mở khóa lệnh này, cho phép thợ mang lệnh đi xuống kho rút đồ.
6. Bấm **Save**. Hệ thống sinh ra Order số `40000001`. Đồng thời nó bắn ngầm định một lệnh Reservation (Giữ chỗ 1 cái Vòng bi) sang phân hệ MM.

---

## 📦 Bước 3: Thợ Nhận Đồ Dưới Kho (T-Code: MIGO)
Thợ sửa chữa cầm phiếu in Work Order xuống kho xin lấy 1 cái Vòng bi.
1. Thủ kho vào T-Code **`MIGO`**.
2. Chọn **Goods Issue** (Xuất kho) -> Theo **Order**.
3. Điền mã Order `40000001`. Bấm Enter. SAP lôi vật tư V-001 lên màn hình.
4. Chọn Kho xuất (`K001`). Tick **Item OK**.
5. Bấm **Post**.
*(Lúc này, tiền của 1 cái vòng bi (VD: 5 Triệu) đã bị trừ khỏi kho MM và "chảy" vào nằm trong Order 40000001. Đây là Vật tư phí).*

---

## ⏱️ Bước 4: Báo Cáo Hoàn Thành Giờ Công (T-Code: IW41)
Thợ lắp vòng bi xong, máy chạy mượt. Trở về văn phòng báo công.
1. Thợ vào T-Code **`IW41`** (Time Confirmation).
2. Điền số Order `40000001`. Bấm Enter.
3. Ở ô **Actual Work** (Giờ thực tế), nhập `3` Giờ (Thực tế làm mất 3 tiếng chứ không phải 2 tiếng như dự kiến).
4. Check vào ô **Final Confirmation** (Hoàn tất công việc).
5. Bấm **Save**.
*(Hệ thống lập tức gọi qua CO, lấy đơn giá lương của thợ cơ khí (VD: 100k/giờ) nhân với 3 giờ = 300k. Số tiền 300k Nhân công này được nhét vào Order 40000001).*

---

## 🔒 Bước 5: Đóng Lệnh Và Quyết Toán (T-Code: IW32 / KO88)
1. Tổ trưởng nghiệm thu, vào lại `IW32` sửa Order `40000001`. Nhấn nút **TECO (Technically Completed)** trên thanh công cụ. Máy tính khóa không cho xuất thêm đồ hay báo công vào Order này nữa.
2. Cuối tháng, Kế toán giá thành (CO) chạy T-Code **`KO88`** (Settlement).
3. Lệnh này sẽ rút toàn bộ (5 triệu vật tư + 300k tiền công) đang nằm tạm trong Order 40000001, và ném "bịch" vào **Cost Center** của xưởng đang xài cái máy Bơm P-001 đó.
4. Giám đốc mở báo cáo xưởng tháng này, sẽ thấy khoản chi phí "Sửa chữa sự cố đột xuất: 5,300,000 VND". Rất rõ ràng!
