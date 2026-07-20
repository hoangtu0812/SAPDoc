# Lộ Trình Học ABAP Từ Cơ Bản Đến Nâng Cao

Lộ trình này được thiết kế để dẫn dắt bạn từ một người chưa biết gì về ABAP đến khi có thể tham gia vào các dự án SAP thực tế. Mỗi phần đều đi kèm với các khái niệm cần học và bài tập thực hành.

---

## Bài 1: ABAP Cơ Bản (ABAP Fundamentals)
Mục tiêu: Làm quen với môi trường code và các cú pháp cơ bản nhất.

* **Kiến thức cần học:**
  * Giới thiệu về SAP kiến trúc 3 lớp, T-Code SE38.
  * Các kiểu dữ liệu cơ bản (C, N, I, P, D, T, String).
  * Khai báo biến (`DATA`), hằng số (`CONSTANTS`).
  * Cấu trúc điều kiện (`IF...ELSE`, `CASE...WHEN`).
  * Vòng lặp (`DO...ENDDO`, `WHILE...ENDWHILE`).
  * Xử lý chuỗi (String operations: `CONCATENATE`, `SPLIT`, `REPLACE`).
* **Bài tập thực hành:** 
  1. Viết chương trình máy tính bỏ túi (Cộng, trừ, nhân, chia) nhận đầu vào từ màn hình (Parameters).
  2. Viết chương trình in ra bảng cửu chương.

---

## Bài 2: Từ Điển Dữ Liệu (ABAP Dictionary - T-Code SE11)
Mục tiêu: Hiểu cách SAP lưu trữ dữ liệu và cách tự tạo bảng trong database.

* **Kiến thức cần học:**
  * Domain (Miền giá trị) & Data Element (Thành phần dữ liệu).
  * Database Table (Bảng CSDL): Tạo bảng, Khóa chính (Primary Key), Technical Settings.
  * Table Type, Structures.
  * Foreign Keys (Khóa ngoại) & Value Table.
  * Views (Database View, Projection View...).
  * Search Help (Tạo F4 Help cho các trường nhập liệu).
* **Bài tập thực hành:**
  1. Tạo bảng `ZNHANVIEN` (Mã NV, Tên NV, Ngày Sinh, Bộ Phận) và `ZBOPHAN` (Mã BP, Tên BP).
  2. Tạo liên kết khóa ngoại giữa `ZNHANVIEN` và `ZBOPHAN`.
  3. Thêm dữ liệu thủ công vào bảng thông qua màn hình bảo trì (Table Maintenance Generator - SM30).

---

## Bài 3: Xử lý Dữ Liệu (Internal Tables & Open SQL)
Mục tiêu: Lấy dữ liệu từ Database ra bộ nhớ tạm để xử lý (Kỹ năng quan trọng bậc nhất của ABAPer).

* **Kiến thức cần học:**
  * Khái niệm Work Area và Internal Table.
  * Các loại Internal Table: Standard, Sorted, Hashed.
  * Thao tác với Internal Table: `APPEND`, `INSERT`, `READ TABLE`, `LOOP AT`, `MODIFY`, `DELETE`.
  * Open SQL cơ bản: `SELECT SINGLE`, `SELECT...INTO TABLE`.
  * Open SQL nâng cao: `INNER JOIN`, `LEFT OUTER JOIN`, `FOR ALL ENTRIES`.
* **Bài tập thực hành:**
  1. Viết chương trình lấy toàn bộ dữ liệu từ bảng `ZNHANVIEN` đã tạo ở Bài 2, lọc ra những nhân viên thuộc bộ phận "IT" và in ra màn hình.
  2. Join dữ liệu từ 2 bảng hệ thống của SAP (ví dụ: `MARA` - Thông tin vật tư chung và `MAKT` - Mô tả vật tư) để in ra danh sách mã vật tư kèm theo tên của nó.

---

## Bài 4: Lập Trình Theo Module (Modularization)
Mục tiêu: Chia nhỏ code để dễ quản lý và tái sử dụng.

* **Kiến thức cần học:**
  * Subroutines (`FORM...PERFORM`).
  * Includes (Chia file code).
  * Function Modules & Function Groups (T-Code SE37).
  * Khái niệm truyền tham số: Pass by value vs Pass by reference.
* **Bài tập thực hành:**
  1. Chuyển logic tính toán của bài tập Máy tính bỏ túi thành một Function Module trong SE37.
  2. Gọi Function Module đó vào trong chương trình chính bằng lệnh `CALL FUNCTION`.

---

## Bài 5: Báo Cáo Chuyên Nghiệp (ALV Reports)
Mục tiêu: Làm ra các báo cáo có giao diện bảng biểu đẹp mắt, có thể lọc, sắp xếp, xuất Excel.

* **Kiến thức cần học:**
  * Selection Screen: `PARAMETERS`, `SELECT-OPTIONS`, `SELECTION-SCREEN BLOCK`.
  * Classic Report & Interactive Report (Events: `START-OF-SELECTION`, `AT LINE-SELECTION`).
  * **ALV Grid Report** (Quan trọng): Dùng Function Module `REUSE_ALV_GRID_DISPLAY`.
  * Object-Oriented ALV (Sử dụng `CL_SALV_TABLE` hoặc `CL_GUI_ALV_GRID`).
* **Bài tập thực hành:**
  1. Viết báo cáo hiển thị danh sách Đơn hàng bán (Sales Orders) lấy từ bảng `VBAK` và `VBAP`.
  2. Báo cáo phải hiển thị dưới dạng ALV.
  3. Thêm chức năng double-click vào một dòng để chuyển sang màn hình xem chi tiết đơn hàng (Sử dụng T-Code `VA03`).

---

## Bài 6: ABAP Hướng Đối Tượng (ABAP Objects)
Mục tiêu: Viết code theo tiêu chuẩn hướng đối tượng (OOP).

* **Kiến thức cần học:**
  * Lớp (Classes) và Đối tượng (Objects). Global Class (SE24) vs Local Class.
  * Thuộc tính (Attributes) & Phương thức (Methods). Visibility: Public, Private, Protected.
  * Kế thừa (Inheritance).
  * Đa hình (Polymorphism) & Giao diện (Interfaces).
  * Bắt lỗi (Exception Handling) bằng `TRY...CATCH`.
* **Bài tập thực hành:**
  1. Tạo một Class trong SE24 đại diện cho "Tài khoản ngân hàng" với các phương thức Rút tiền, Gửi tiền, Kiểm tra số dư.
  2. Viết chương trình gọi Class này và xử lý ngoại lệ nếu số tiền rút lớn hơn số dư.

---

## Bài 7: Giao diện và Biểu mẫu (Screens & Forms)
Mục tiêu: Thiết kế màn hình thao tác tùy chỉnh và các biểu mẫu in ấn (Hóa đơn, phiếu xuất kho...).

* **Kiến thức cần học:**
  * Module Pool Programming (T-Code SE51): Tạo Custom Screens, PAI (Process After Input), PBO (Process Before Output).
  * SmartForms (T-Code SMARTFORMS).
  * SAP Adobe Forms (SFP - Xu hướng hiện nay).
* **Bài tập thực hành:**
  1. Thiết kế một màn hình (Screen) gồm các ô nhập liệu cho phép người dùng Thêm/Sửa/Xóa dữ liệu trực tiếp vào bảng `ZNHANVIEN`.
  2. Thiết kế một SmartForm hiển thị thông tin hóa đơn và gọi nó từ một chương trình ABAP.

---

## Bài 8: Giao Tiếp và Chuyển Đổi Dữ Liệu (Data Migration / Interfaces)
Mục tiêu: Đưa dữ liệu hàng loạt từ Excel vào SAP hoặc giao tiếp với hệ thống ngoài.

* **Kiến thức cần học:**
  * BDC (Batch Data Communication - T-Code SHDB): Record màn hình để import data tự động (Call Transaction).
  * BAPI (Business Application Programming Interface - BAPI).
  * RFC (Remote Function Call): Cho phép hệ thống khác gọi vào SAP.
  * IDoc / ALE (Electronic Data Interchange): Truyền nhận chứng từ giữa 2 hệ thống SAP.
* **Bài tập thực hành:**
  1. Dùng SHDB record lại quá trình tạo một Vật tư mới (T-Code MM01).
  2. Viết chương trình đọc file Excel chứa 100 vật tư và dùng BDC chạy tự động để tạo hàng loạt vật tư này vào hệ thống.

---

## Bài 9: Cải Tiến Hệ Thống (Enhancements)
Mục tiêu: Can thiệp và thay đổi logic chuẩn của SAP mà không làm hỏng code gốc của hãng.

* **Kiến thức cần học:**
  * Khái niệm Enhancement Framework.
  * Implicit / Explicit Enhancements.
  * User Exits / Customer Exits (SMOD/CMOD).
  * BAdI (Business Add-Ins - T-Code SE18/SE19).
* **Bài tập thực hành:**
  1. Tìm một BAdI được kích hoạt khi lưu Đơn hàng (Sales Order).
  2. Viết code vào BAdI đó để kiểm tra: "Nếu giá trị đơn hàng > 1 tỷ, hiển thị cảnh báo (Warning)".

---

## Bài 10: ABAP Hiện Đại (Advanced/Modern ABAP) - *Tương lai*
Mục tiêu: Cập nhật các công nghệ mới nhất của SAP (SAP S/4HANA).

* **Kiến thức cần học:**
  * ABAP for SAP HANA (Cú pháp ABAP 7.4+ trở lên: Inline declarations, NEW, VALUE...).
  * Core Data Services (CDS Views).
  * AMDP (ABAP Managed Database Procedures).
  * OData Services (T-Code SEGW - Cung cấp API cho các ứng dụng Web/Mobile).
  * Cơ bản về SAP Fiori.

