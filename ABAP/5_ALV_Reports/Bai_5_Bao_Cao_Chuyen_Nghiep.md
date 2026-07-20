# 📊 Bài 5: Báo Cáo Chuyên Nghiệp (ALV Reports)

**Mục tiêu:** Thoát khỏi màn hình in đen trắng nhàm chán của lệnh `WRITE`, làm ra các báo cáo dạng bảng biểu đẹp mắt, có thể lọc, sắp xếp, và xuất Excel.

---

## 📚 Kiến thức cần học
* 🎛️ **Selection Screen (Màn hình chọn lọc):** 
  * `PARAMETERS` (Chọn 1 giá trị).
  * `SELECT-OPTIONS` (Chọn nhiều giá trị, khoảng giá trị).
  * `SELECTION-SCREEN BLOCK` (Kẻ khung, gom nhóm tiêu chí lọc).
* ⏰ **Events (Sự kiện) trong Báo cáo:** 
  * `INITIALIZATION` (Khởi tạo mặc định).
  * `AT SELECTION-SCREEN` (Kiểm tra dữ liệu nhập).
  * `START-OF-SELECTION` (Bắt đầu lấy dữ liệu).
* 🏆 **ALV Grid Report (Cực kỳ quan trọng):** 
  * Xây dựng Field Catalog (Định nghĩa cột, tiêu đề).
  * Dùng Function Module kinh điển: `REUSE_ALV_GRID_DISPLAY`.
* 💎 **Object-Oriented ALV:** Sử dụng Class `CL_SALV_TABLE` hoặc `CL_GUI_ALV_GRID` (Hiện đại hơn).

---

## 💻 Bài tập thực hành

### 📝 Bài tập: Báo cáo Đơn hàng bán (Sales Orders)
1. **Thiết kế Selection Screen:** Nhập dải mã Khách hàng (Customer - trường `KUNNR` trong `VBAK`).
2. **Lấy dữ liệu:** Join bảng `VBAK` (Header đơn hàng) và `VBAP` (Chi tiết đơn hàng).
3. **Hiển thị:** Dùng `REUSE_ALV_GRID_DISPLAY` xuất kết quả ra màn hình ALV.
4. **Nâng cao (Interactive):** Bắt sự kiện double-click (`USER_COMMAND`) vào dòng báo cáo để gọi T-Code `VA03` (Xem chi tiết đơn hàng bán) tương ứng với dòng vừa click.

