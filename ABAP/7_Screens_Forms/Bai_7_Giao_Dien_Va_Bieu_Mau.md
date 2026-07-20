# 🖥️ Bài 7: Giao Diện và Biểu Mẫu (Screens & Forms)

**Mục tiêu:** Thiết kế màn hình thao tác tùy chỉnh (Dialog) cho người dùng và thiết kế các biểu mẫu in ấn như Hóa đơn, Phiếu xuất kho, Bảng lương...

---

## 📚 Kiến thức cần học
* 🖼️ **Module Pool Programming (T-Code SE51):** 
  * Tạo Custom Screens (Screen Painter).
  * PAI (Process After Input): Xử lý dữ liệu khi user nhấn nút.
  * PBO (Process Before Output): Cài đặt màn hình trước khi hiển thị cho user.
* 🖨️ **Biểu mẫu in ấn (Forms):**
  * **SAPscripts:** Công nghệ cũ, chỉ nên biết sơ để duy trì hệ thống cũ.
  * **SmartForms (T-Code SMARTFORMS):** Phổ biến nhất trong các hệ thống SAP ECC hiện tại. Giao diện kéo thả trực quan.
  * **SAP Adobe Forms (SFP):** Xu hướng hiện nay (yêu cầu cài đặt Adobe LiveCycle Designer).

---

## 💻 Bài tập thực hành

### 📝 Bài tập 1: Màn hình quản lý Nhân Viên
1. Tạo một Module Pool Program (Loại M).
2. Dùng SE51 thiết kế một màn hình gồm các Input fields để nhập thông tin nhân viên (Mã NV, Tên NV, Ngày sinh...).
3. Thêm 3 nút: Save, Clear, Exit.
4. Viết logic: Khi nhấn Save, lưu thông tin nhập trên màn hình vào Database Table `ZNHANVIEN`.

### 📝 Bài tập 2: SmartForm Phiếu Mua Hàng
1. Tạo một SmartForm hiển thị thông tin hóa đơn/phiếu mua hàng (Có logo công ty, thông tin chung, và bảng chi tiết mặt hàng).
2. Viết một chương trình ABAP gọi SmartForm này, truyền dữ liệu từ Internal Table vào Form để hiển thị (Print Preview).

