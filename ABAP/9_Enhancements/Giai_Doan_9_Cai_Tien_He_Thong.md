# 🛠️ Giai đoạn 9: Cải Tiến Hệ Thống (Enhancements)

**Mục tiêu:** Học cách can thiệp, thêm logic cá nhân hóa của doanh nghiệp vào quy trình chuẩn của SAP mà **không** làm hỏng code gốc của hãng.

---

## 📚 Kiến thức cần học
* 🧬 **Khái niệm Enhancement Framework:** Tại sao SAP không cho phép sửa trực tiếp code chuẩn?
* 📍 **Implicit / Explicit Enhancements:** 
  * Cách tìm và chèn code vào đầu/cuối của một hàm chuẩn (Implicit).
  * Enhancement Spots (Explicit).
* 🚪 **User Exits / Customer Exits (Công nghệ cũ):** 
  * T-Code SMOD, CMOD.
  * Các Exit thường nằm trong Function Modules bắt đầu bằng `EXIT_`.
* 🔌 **BAdI (Business Add-Ins - Công nghệ mới nhất):** 
  * Dựa trên nền tảng OOP (Interfaces).
  * T-Code SE18 (Định nghĩa BAdI) và SE19 (Implement BAdI).

---

## 💻 Bài tập thực hành

### 📝 Bài tập: Cảnh báo khi lưu Đơn hàng (BAdI)
1. Giả sử yêu cầu của công ty là: "Khi nhân viên kinh doanh tạo đơn hàng bán (T-Code VA01), nếu tổng giá trị đơn hàng vượt quá 1 Tỷ VNĐ, hệ thống phải hiện ra một cảnh báo (Warning message)".
2. Nhiệm vụ của bạn: Tìm một BAdI phù hợp được kích hoạt lúc lưu Đơn hàng.
3. Dùng SE19 để tạo Implementation cho BAdI đó.
4. Viết code logic: Lấy tổng tiền, kiểm tra điều kiện > 1 tỷ, nếu đúng thì phát ra `MESSAGE 'Đơn hàng giá trị lớn, cần phê duyệt!' TYPE 'W'`.
