# 🗄️ Giai đoạn 2: Từ Điển Dữ Liệu (ABAP Dictionary)

**Mục tiêu:** Hiểu cách SAP lưu trữ dữ liệu và cách tự tạo bảng trong database thông qua T-Code **SE11**.

---

## 📚 Kiến thức cần học
* 🎯 **Domain (Miền giá trị):** Định nghĩa kiểu dữ liệu kỹ thuật và độ dài.
* 🧩 **Data Element (Thành phần dữ liệu):** Định nghĩa ngữ nghĩa và nhãn (label) hiển thị trên màn hình.
* 📊 **Database Table (Bảng CSDL):** 
  * Cách tạo bảng Z*.
  * Khóa chính (Primary Key).
  * Technical Settings (Data class, Size category).
  * Table Type, Structures.
* 🔗 **Khóa ngoại (Foreign Keys) & Value Table:** Ràng buộc toàn vẹn dữ liệu giữa các bảng.
* 👁️ **Views (Góc nhìn dữ liệu):** Database View, Projection View, Maintenance View.
* 🔍 **Search Help (F4 Help):** Tạo danh sách sổ xuống hỗ trợ người dùng chọn giá trị khi nhập liệu.

---

## 💻 Bài tập thực hành

### 📝 Bài tập 1: Khởi tạo dữ liệu
1. Dùng SE11 tạo bảng `ZNHANVIEN` gồm các trường:
   - `MANV` (Mã NV - Khóa chính)
   - `TENNV` (Tên NV)
   - `NGAYSINH` (Ngày Sinh)
   - `MABP` (Mã Bộ Phận)
2. Dùng SE11 tạo bảng `ZBOPHAN` gồm các trường:
   - `MABP` (Mã BP - Khóa chính)
   - `TENBP` (Tên BP)

### 📝 Bài tập 2: Liên kết và Nhập liệu
1. Tạo liên kết khóa ngoại (Foreign Key) trường `MABP` của bảng `ZNHANVIEN` trỏ tới bảng `ZBOPHAN`.
2. Tạo Table Maintenance Generator (T-Code SM30) cho cả 2 bảng.
3. Vào SM30, thêm 3 bộ phận vào bảng `ZBOPHAN` và 5 nhân viên vào bảng `ZNHANVIEN`.
