# 💎 Bài 11: Tiêu Chuẩn Viết Code (ABAP Best Practices & Clean Code)

**Mục tiêu:** Giúp bạn viết ra những dòng code không chỉ "chạy được" mà còn "đẹp", chuyên nghiệp, dễ bảo trì và dễ hiểu cho những người vào sau.

---

## 1. Quy tắc đặt tên (Naming Conventions)
Trong ABAP, việc đặt tên biến rất quan trọng để người đọc có thể biết ngay phạm vi (scope) và kiểu dữ liệu của biến đó.

* **Tiền tố phạm vi (Scope prefix):**
  * `l` (Local): Biến cục bộ, chỉ dùng trong một Form/Method.
  * `g` (Global): Biến toàn cục, dùng toàn chương trình (Nên hạn chế tối đa).
  * `p` (Parameter): Tham số truyền vào từ màn hình.
  * `s` (Select-Option): Khoảng giá trị nhập từ màn hình.
  * `i` / `e` / `c` (Importing / Exporting / Changing): Tham số truyền vào hàm.

* **Tiền tố kiểu dữ liệu (Type prefix):**
  * `v` (Variable): Biến đơn lẻ (Ví dụ: `lv_name`, `gv_count`).
  * `s` (Structure): Cấu trúc / Work Area (Ví dụ: `ls_employee`).
  * `t` (Table): Internal Table (Ví dụ: `lt_employee`).
  * `r` (Reference): Biến tham chiếu đối tượng (Ví dụ: `lr_alv_grid`).
  * `o` (Object): Đối tượng class (Ví dụ: `lo_bank`).

* **Quy tắc đặt tên Object trong hệ thống:**
  * Bảng tự tạo: `Z<Tên>` hoặc `Y<Tên>` (Ví dụ: `ZNHANVIEN`).
  * Lớp (Class): `ZCL_<Tên>` (Ví dụ: `ZCL_BANK_ACCOUNT`).
  * Interface: `ZIF_<Tên>`.

---

## 2. Định dạng code (Formatting & Pretty Printer)
* **Luôn sử dụng Pretty Printer (Shift + F1):** Công cụ này tự động thụt lề (indent) và chuyển đổi chữ hoa/thường chuẩn xác.
* **Quy định chung:** 
  * Keyword của SAP (SELECT, DATA, IF, LOOP...) -> **Viết Hoa**.
  * Tên biến, tên bảng do người dùng tạo -> **Viết thường**.
* **Comment code (Ghi chú):**
  * Bắt buộc có Header Comment ở đầu mỗi chương trình (Tác giả, Ngày tạo, Mô tả).
  * Giải thích **"Tại sao"** lại làm vậy, chứ không phải giải thích lệnh đó làm gì (đừng comment `* Cộng a cho b` vào lệnh `c = a + b`).

---

## 3. 🔴 Bad Coding vs 🟢 Good Coding

### Ví dụ 1: Gán giá trị cứng (Hardcoding)
* 🔴 **Bad:** `IF lv_status = '01'. "01 là trạng thái Đã duyệt` (Nếu sau này đổi trạng thái thành 'A', bạn phải dò tìm và sửa lại toàn bộ code).
* 🟢 **Good:** Khai báo hằng số: 
  ```abap
  CONSTANTS: gc_status_approved TYPE c LENGTH 2 VALUE '01'.
  IF lv_status = gc_status_approved.
  ```

### Ví dụ 2: Lấy dữ liệu từ Database (SELECT)
* 🔴 **Bad:** Lấy toàn bộ cột dữ liệu dù chỉ cần 2 cột.
  ```abap
  SELECT * FROM mara INTO TABLE lt_mara.
  ```
* 🟢 **Good:** Chỉ `SELECT` những cột thực sự cần thiết để giảm tải đường truyền và RAM.
  ```abap
  SELECT matnr, mtart FROM mara INTO TABLE @DATA(lt_mara).
  ```

### Ví dụ 3: Kiểm tra tính hợp lệ của câu lệnh (SY-SUBRC)
* 🔴 **Bad:** Đọc bảng (READ TABLE) xong, gán giá trị luôn mà không kiểm tra xem nó có đọc thành công không. Điều này sẽ gây lỗi dump hệ thống.
* 🟢 **Good:** Luôn bắt `sy-subrc`.
  ```abap
  READ TABLE lt_mara INTO ls_mara WITH KEY matnr = lv_matnr.
  IF sy-subrc = 0.
    " Đọc thành công, xử lý dữ liệu
  ELSE.
    " Không tìm thấy, báo lỗi
  ENDIF.
  ```

### Ví dụ 4: Khai báo biến (Modern ABAP)
* 🔴 **Cũ:** Khai báo cả một lô biến ở đầu chương trình, làm người đọc rất khó theo dõi biến đó dùng ở đâu.
* 🟢 **Good:** Dùng **Inline Declarations** (của ABAP 7.4) khai báo biến ngay tại thời điểm nó được sử dụng.
  ```abap
  READ TABLE lt_mara INTO DATA(ls_mara) WITH KEY matnr = '1000'.
  ```
