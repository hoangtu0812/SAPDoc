# 🧩 Giai đoạn 4: Lập Trình Theo Module (Modularization)

**Mục tiêu:** Chia nhỏ code thành các khối chức năng riêng biệt để dễ quản lý, dễ đọc và có thể tái sử dụng ở nhiều nơi.

---

## 📚 Kiến thức cần học
* 📦 **Subroutines (`FORM...PERFORM`):** Các chương trình con dùng nội bộ trong một file code (Dù cũ nhưng vẫn còn được dùng rất nhiều).
* 📄 **Includes:** Cách bóc tách một chương trình lớn thành nhiều file nhỏ (Ví dụ: 1 file chứa biến, 1 file chứa giao diện, 1 file chứa logic...).
* ⚙️ **Function Modules & Function Groups (T-Code SE37):** 
  * Tạo các hàm dùng chung cho toàn hệ thống.
  * Phân biệt Import, Export, Changing, và Tables parameters.
* 🔄 **Truyền tham số:** 
  * Pass by value (Truyền tham trị - Giá trị được copy).
  * Pass by reference (Truyền tham chiếu - Truyền địa chỉ vùng nhớ).

---

## 💻 Bài tập thực hành

### 📝 Bài tập 1: Function Module Tính Toán
1. Tạo một Function Group tên `ZCALCULATOR`.
2. Trong đó, tạo một Function Module (T-Code SE37) tên `Z_MATH_CALC` để thực hiện phép tính (+, -, *, /) như ở Bài 1.
3. Import: `SO_A`, `SO_B`, `PHEP_TINH`.
4. Export: `KET_QUA`.
5. Exceptions: Bắt lỗi chia cho 0 (`DIVIDE_BY_ZERO`).

### 📝 Bài tập 2: Tích hợp vào chương trình chính
Viết một chương trình mới (SE38), tạo màn hình nhập liệu và gọi Function Module `Z_MATH_CALC` vừa tạo bằng lệnh `CALL FUNCTION` để in kết quả ra màn hình.
