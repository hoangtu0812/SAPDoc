# 🧱 Giai đoạn 6: ABAP Hướng Đối Tượng (ABAP Objects)

**Mục tiêu:** Viết code theo tiêu chuẩn hướng đối tượng (OOP). Đây là xu hướng bắt buộc đối với lập trình viên ABAP hiện tại và tương lai.

---

## 📚 Kiến thức cần học
* 🏛️ **Lớp (Classes) và Đối tượng (Objects):** 
  * Cách tạo Global Class (T-Code SE24).
  * Cách viết Local Class ngay trong chương trình SE38.
* 🔐 **Thuộc tính (Attributes) & Phương thức (Methods):** 
  * Phạm vi truy cập (Visibility): `Public`, `Private`, `Protected`.
  * Khởi tạo đối tượng (`CREATE OBJECT` hoặc `NEW`).
* 🧬 **Tính Kế thừa (Inheritance):** Kế thừa từ Superclass.
* 🎭 **Đa hình (Polymorphism) & Giao diện (Interfaces):** Triển khai interface (`INTERFACES ...`).
* ⚠️ **Xử lý Ngoại lệ (Exception Handling):** Bắt lỗi với cấu trúc `TRY...CATCH...ENDTRY`.

---

## 💻 Bài tập thực hành

### 📝 Bài tập: Class Quản lý Tài Khoản Ngân Hàng
1. Vào SE24 tạo một Global Class có tên `ZCL_BANK_ACCOUNT`.
2. Tạo các thuộc tính (Private): `Số tài khoản`, `Số dư`.
3. Tạo các phương thức (Public):
   - `DEPOSIT` (Gửi tiền vào).
   - `WITHDRAW` (Rút tiền ra).
   - `GET_BALANCE` (Lấy số dư hiện tại).
4. Viết logic cho hàm Rút tiền: Nếu số tiền rút lớn hơn số dư, ném ra một Exception (Tự tạo một Exception Class `ZCX_INSUFFICIENT_FUNDS`).
5. Tạo một chương trình SE38, khởi tạo đối tượng từ Class này, thực hiện chuỗi lệnh gửi/rút tiền và in kết quả/thông báo lỗi ra màn hình.
