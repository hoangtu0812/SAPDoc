# 📦 Bài 4: Chuyên Sâu Về Release Strategy (Chiến Lược Phê Duyệt MM)

Một trong những cấu hình "hack não" nhưng được sử dụng 100% trong tất cả các dự án triển khai SAP MM chính là **Release Strategy (Chiến lược phê duyệt)** cho Yêu cầu mua hàng (PR) và Đơn đặt hàng (PO).

Bài viết này giải phẫu cơ chế hoạt động của tính năng này.

---

## 1. Yêu Cầu Bài Toán Thực Tế
Ví dụ quy trình của công ty bạn là:
* Nếu nhân viên mua hàng tạo PO có tổng giá trị **< 50 Triệu VNĐ**: Chỉ cần Trưởng phòng Mua hàng (Manager) duyệt là PO có hiệu lực gửi cho nhà cung cấp.
* Nếu PO có giá trị **>= 50 Triệu VNĐ**: Cần Trưởng phòng (Manager) duyệt, sau đó Giám đốc (Director) duyệt lớp thứ 2 thì mới có hiệu lực.

SAP MM giải quyết bài toán này bằng ma trận Characteristic (Đặc tính) và Class (Lớp).

---

## 2. Các Thành Phần Của Release Strategy

### 1. Characteristic (Đặc tính) - T-Code `CT04`
Đặc tính chính là các **tiêu chí** để hệ thống bám vào đó mà quyết định xem có đưa vào luồng duyệt hay không.
* Trong ví dụ trên, tiêu chí là **"Tổng giá trị PO"**.
* Trong `CT04`, ta tạo một Đặc tính tên `Z_PO_NET_VALUE`. Ánh xạ nó vào trường `CEKKO-GNETW` (Trường tổng giá trị PO trong cấu trúc dữ liệu của SAP).

### 2. Class (Lớp) - T-Code `CL02`
Một Lớp dùng để gom nhiều Đặc tính lại với nhau. (Bởi vì quy trình duyệt thực tế thường kết hợp nhiều điều kiện: Giá trị > 50tr VÀ Nhóm hàng hóa là Máy móc).
* Trong `CL02`, tạo một Class (Loại class 032 - Release Strategy). Gắn đặc tính `Z_PO_NET_VALUE` vào class này.

### 3. Release Group & Release Code (Nhóm và Mã phê duyệt)
Thực hiện trong **`SPRO`** (`Materials Management -> Purchasing -> Purchase Order -> Release Procedure for Purchase Orders`).
* **Release Code:** Chính là các "Vai trò" tham gia duyệt. 
  * `M1` = Manager (Trưởng phòng).
  * `D1` = Director (Giám đốc).

### 4. Release Strategy (Ma trận duyệt)
Tại đây bạn lắp ghép mọi thứ lại:
* **Chiến lược 1 (PO < 50tr):**
  * Gắn mã duyệt: Chỉ có `M1`.
  * Classification (Giá trị điều kiện): Mở màn hình ra, nhập `0 - 49,999,999` vào đặc tính `Z_PO_NET_VALUE`.
* **Chiến lược 2 (PO >= 50tr):**
  * Gắn mã duyệt: Gồm `M1` và `D1`.
  * Khai báo Prerequisites (Thứ tự duyệt): Phải duyệt `M1` trước, thì `D1` mới hiện ra cho duyệt.
  * Classification: Nhập `>= 50,000,000`.

---

## 3. Trải Nghiệm Của Người Dùng (User Experience)

Sau khi cấu hình xong, quy trình diễn ra như sau:
1. Nhân viên gõ lệnh **`ME21N`** tạo PO trị giá 100 Triệu. Bấm Save.
2. PO này lập tức bị "Khóa" (Blocked). Trên góc phải màn hình PO xuất hiện một tab mới tên là **Release Strategy**. Tab này hiển thị cần 2 chữ ký: `M1` và `D1`.
3. Trưởng phòng đăng nhập SAP, vào T-Code **`ME29N`** (Release PO) hoặc `ME28` (Duyệt hàng loạt). Nhấn nút duyệt (Approve) cho mã `M1`.
4. Lúc này Giám đốc mới thấy nút duyệt của mình hiện lên. Giám đốc vào duyệt `D1`.
5. Đơn hàng hoàn tất phê duyệt, trạng thái chuyển sang Mở. Nhân viên mua hàng lúc này mới có thể in đơn hàng PDF gửi cho Vendor (Message Output).
