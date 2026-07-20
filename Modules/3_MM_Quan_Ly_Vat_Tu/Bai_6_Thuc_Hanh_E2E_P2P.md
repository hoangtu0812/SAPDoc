# 📦 Bài 6: Thực Hành End-to-End: Quy Trình P2P (Mua Hàng - Nhập Kho - Trả Tiền)

Bài này hướng dẫn bạn thao tác chính xác luồng Procure To Pay trên hệ thống SAP từ góc độ của bộ phận Mua Hàng (MM), Kho (MM) và Kế toán công nợ (FI).

---

## 🛒 Bước 1: Tạo Yêu Cầu Mua Hàng - PR (T-Code: ME51N)
Phân xưởng sản xuất đang cạn kiệt nguyên liệu, họ yêu cầu phòng thu mua đi mua 100 cái Bánh răng.
1. Vào T-Code **`ME51N`**.
2. Ở bảng Item detail, điền:
   * **Material:** Nhập mã Bánh răng (VD: `RM-001`).
   * **Quantity:** `100`.
   * **Plant:** `VN01`.
3. Bấm **Save**. Hệ thống sinh ra số PR: `10000001`. 
*(Lệnh này chỉ lưu nội bộ, không có bút toán kế toán).*

---

## 📜 Bước 2: Tạo Đơn Đặt Hàng - PO (T-Code: ME21N)
Phòng Thu mua nhận được PR, liên hệ nhà cung cấp "Công ty Cơ khí A" chốt giá 10$ / cái, và lập Đơn đặt hàng (PO).
1. Vào T-Code **`ME21N`**.
2. **Vendor:** Nhập mã `V-001` (Công ty Cơ khí A).
3. **Purchasing Org:** `PO01`, **Purchasing Group:** `001`.
4. Bật thanh menu bên trái (Document Overview On), tìm kiếm PR số `10000001`, kéo thả nó vào giỏ hàng (Icon xe đẩy) để **Copy dữ liệu** (Đỡ phải gõ lại mã vật tư).
5. Ở bảng chi tiết, nhập thêm **Net Price** (Giá chốt): `10`.
6. Bấm **Save**. Hệ thống sinh ra số PO: `4500000001`. In file PDF này gửi cho Vendor.

---

## 🚚 Bước 3: Phê Duyệt Đơn Đặt Hàng (T-Code: ME29N)
*(Nếu hệ thống có cấu hình Release Strategy cho PO).*
1. Giám đốc vào T-Code **`ME29N`**.
2. Điền số PO `4500000001`.
3. Chuyển sang Tab **Release Strategy** ở khung Header.
4. Bấm vào biểu tượng "Dấu Tick màu vàng" để Approve. Bấm Save. Trạng thái PO mở khóa.

---

## 🏭 Bước 4: Nhập Kho - Goods Receipt (T-Code: MIGO)
Vài ngày sau, xe tải chở hàng tới cổng nhà máy. Thủ kho kiểm tra đúng 100 cái bánh răng.
1. Vào T-Code **`MIGO`**.
2. Khung điều hướng chọn: **Goods Receipt** (Ô thứ 1) - Theo **Purchase Order** (Ô thứ 2).
3. Điền số PO `4500000001` vào ô bên cạnh và bấm Enter. Hệ thống tự kéo dữ liệu vật tư và số lượng lên.
4. Di chuyển xuống khung Detail Data, tab **Where**, nhập **Storage Location** (Kho chứa): `K001`.
5. Quan trọng nhất: Tick vào ô **Item OK** ở dưới cùng màn hình để xác nhận dòng này đã kiểm tra.
6. Bấm **Post** (Lưu). Sinh ra số phiếu nhập kho Material Document: `5000000001`.
*(Lúc này SAP tự động đánh bút toán FI thông qua OBYC: Nợ Hàng tồn kho 1,000$ / Có TK Trung gian GR/IR 1,000$).*

---

## 🧾 Bước 5: Ghi Nhận Hóa Đơn - Invoice Verification (T-Code: MIRO)
Kế toán cầm trên tay Hóa đơn đỏ do Vendor gửi tới, giá trị 1,000$ + 100$ VAT = 1,100$. Kế toán đối chiếu với kho xem đã nhận đủ hàng chưa.
1. Vào T-Code **`MIRO`**.
2. **Invoice Date:** Nhập ngày ghi trên hóa đơn đỏ.
3. Ở ô **PO/Scheduling Agreement**, nhập số PO `4500000001` và bấm Enter.
   * Hệ thống sẽ tự động móc nối với MIGO ở Bước 4, tính ra là "Kho đã nhập 100 cái, giá trị 1,000$". Nó sẽ hiện lên ở danh sách bên dưới.
4. Ở ô **Amount** (Phần Header trên cùng), nhập tổng tiền hóa đơn: `1,100`.
5. Check ô **Calculate Tax**, chọn loại thuế 10%.
6. Hệ thống sẽ báo góc trên cùng bên phải chữ **Balance: 0.00 Green (Khớp tiền)**. (1,000$ gốc + 100$ thuế = 1,100$ Tổng tiền).
7. Bấm **Post**. Sinh ra hóa đơn số `5100000001`.
*(Lúc này sinh bút toán: Nợ GR/IR 1,000$ (Triệt tiêu khoản trung gian ở bước 4) / Nợ Thuế 100$ / Có Công nợ Vendor 1,100$).*

---

## 💰 Bước 6: Kế Toán Trả Tiền (T-Code: F-53)
Bước cuối cùng, kế toán dùng T-Code **`F-53`** làm lệnh chi trả 1,100$ từ tài khoản ngân hàng để cấn trừ (Clear) công nợ 1,100$ cho Vendor V-001 (Y hệt như Bài Thực hành E2E FI). Chu trình P2P hoàn tất!
