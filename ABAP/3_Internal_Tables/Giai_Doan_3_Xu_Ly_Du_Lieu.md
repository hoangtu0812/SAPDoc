# 🧠 Giai đoạn 3: Xử Lý Dữ Liệu (Internal Tables & Open SQL)

**Mục tiêu:** Nắm vững kỹ năng lấy dữ liệu từ Database ra bộ nhớ tạm để tính toán, xử lý. Đây là kỹ năng **quan trọng bậc nhất** của một lập trình viên ABAP.

---

## 📚 Kiến thức cần học
* 📦 **Work Area (Vùng làm việc) & Internal Table (Bảng nội):** Nơi chứa dữ liệu tạm thời khi chương trình đang chạy.
* 🗂️ **Các loại Internal Table:** 
  * Standard Table
  * Sorted Table
  * Hashed Table
* 🛠️ **Thao tác với Internal Table:** 
  * Thêm dữ liệu: `APPEND`, `INSERT`.
  * Đọc dữ liệu: `READ TABLE`.
  * Duyệt dữ liệu: `LOOP AT ... ENDLOOP`.
  * Cập nhật/Xóa: `MODIFY`, `DELETE`.
* 🔎 **Open SQL cơ bản:** 
  * `SELECT SINGLE` (Lấy 1 dòng).
  * `SELECT ... INTO TABLE` (Lấy nhiều dòng).
* 🔗 **Open SQL nâng cao:** 
  * `INNER JOIN`, `LEFT OUTER JOIN`.
  * `FOR ALL ENTRIES IN` (Cực kỳ phổ biến để thay thế Join khi kết nối nhiều bảng).

---

## 💻 Bài tập thực hành

### 📝 Bài tập 1: Truy vấn bảng Z
Viết chương trình lấy toàn bộ dữ liệu từ bảng `ZNHANVIEN` (đã tạo ở Giai đoạn 2), đưa vào một Internal Table.
- Dùng `LOOP AT` để lọc ra những nhân viên thuộc bộ phận "IT" và in tên của họ ra màn hình.

### 📝 Bài tập 2: Join dữ liệu hệ thống
Viết chương trình lấy danh sách vật tư từ 2 bảng chuẩn của SAP:
- `MARA` (Chứa thông tin vật tư chung: Mã vật tư `MATNR`, Loại vật tư `MTART`).
- `MAKT` (Chứa mô tả vật tư: Mô tả `MAKTX`).
- Sử dụng `INNER JOIN` để lấy ra 50 mã vật tư kèm theo mô tả của chúng và in ra màn hình (`WRITE`).
