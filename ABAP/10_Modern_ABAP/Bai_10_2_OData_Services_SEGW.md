# 🚀 Bài 10.2: OData Services (T-Code SEGW)

**Mục tiêu:** Hiểu và biết cách tạo ra một OData Service - Trái tim của các ứng dụng SAP Fiori và tích hợp hiện đại.

---

## 1. OData là gì?
**OData (Open Data Protocol)** do Microsoft khởi xướng, được mệnh danh là "SQL cho Web". Nó là một loại REST API đặc biệt, tuân thủ các quy tắc chuẩn mực.
OData cho phép bạn tương tác với dữ liệu qua các phương thức HTTP tiêu chuẩn:
* **GET:** Đọc dữ liệu (Read).
* **POST:** Tạo mới dữ liệu (Create).
* **PUT/PATCH:** Cập nhật dữ liệu (Update).
* **DELETE:** Xóa dữ liệu (Delete).

=> Đây chính là mô hình **CRUD** nổi tiếng.

---

## 2. SAP Gateway & T-Code SEGW
Để biến dữ liệu từ các bảng SAP thành OData trả về định dạng JSON/XML cho Frontend (Fiori/Web) sử dụng, SAP dùng một công cụ gọi là **SAP Gateway Service Builder (T-Code: `SEGW`)**.

### Quy trình tạo OData Service trong SEGW:
1. **Tạo Project (`SEGW`):** Khởi tạo một dự án OData.
2. **Data Model (Định nghĩa dữ liệu):**
   * Định nghĩa **Entity Type** (Tương đương cấu trúc của 1 dòng dữ liệu. VD: `Material`).
   * Định nghĩa **Entity Set** (Tương đương 1 bảng dữ liệu, chứa nhiều dòng. VD: `MaterialSet`).
3. **Generate (Sinh code):** Nhấn nút Generate. Hệ thống tự động sinh ra 2 Class (DPC - Data Provider Class và MPC - Model Provider Class).
4. **Viết Code Logic (DPC_EXT Class):**
   * Redefine method `..._GET_ENTITYSET`: Viết lệnh `SELECT` để trả về danh sách vật tư.
   * Redefine method `..._GET_ENTITY`: Trả về chi tiết 1 vật tư dựa vào khóa (Key).
   * Redefine method `..._CREATE_ENTITY`: Viết lệnh `INSERT` dữ liệu.
5. **Đăng ký Service (`/IWFND/MAINT_SERVICE`):** Kích hoạt service để lấy URL sử dụng. 

### Kiểm tra:
Sử dụng T-Code **`/IWFND/GW_CLIENT`** (Gateway Client) hoặc Postman để gọi thử URL. Nếu thành công, bạn sẽ nhận được chuỗi JSON dữ liệu từ SAP!
