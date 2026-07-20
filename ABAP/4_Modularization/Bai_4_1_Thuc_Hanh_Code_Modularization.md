# 💻 Bài 4.1: Thực Hành Code Chia Module (Subroutines & Functions)

Chia nhỏ chương trình thành các phần (Modularization) giúp code dễ bảo trì. Bạn không bao giờ nên viết một chương trình dài 2000 dòng ở luồng chính (Main program).

### 📝 Đề bài: Viết chương trình bằng các Subroutine (FORM) và gọi một Function Module chuẩn của SAP.

```abap
REPORT z_modularization_demo.

DATA: gv_date_input  TYPE d,
      gv_date_output TYPE string.

PARAMETERS: p_date TYPE d DEFAULT sy-datum. " sy-datum là biến hệ thống lấy Ngày hiện tại

START-OF-SELECTION.
  gv_date_input = p_date.
  
  " 1. Gọi chương trình con (Subroutine) để xử lý logic
  PERFORM convert_date_format USING    gv_date_input
                              CHANGING gv_date_output.

  " 3. Hiển thị kết quả
  WRITE: / 'Ngày gốc:', gv_date_input.
  WRITE: / 'Ngày sau khi format:', gv_date_output.

*----------------------------------------------------------------------*
* Định nghĩa Subroutine (Thường đặt ở cuối file code)
*----------------------------------------------------------------------*
FORM convert_date_format USING    uv_date TYPE d
                         CHANGING cv_formatted_date TYPE string.
  
  " Giải thích tham số:
  " USING: Tham số truyền vào (Input), không được thay đổi giá trị của nó.
  " CHANGING: Tham số trả về (Output), thay đổi giá trị để trả ngược lại ra ngoài.

  DATA: lv_month_name TYPE t247-ltx.

  " 2. Gọi một Function Module (Hàm chuẩn của SAP)
  " FM này truyền vào số tháng (ví dụ 07), sẽ trả ra chữ (ví dụ: July)
  " Cách gọi nhanh: Dùng nút "Pattern" trên menu -> Chọn CALL FUNCTION -> Gõ MONTH_NAMES_GET
  
  CALL FUNCTION 'MONTH_NAMES_GET'
    EXPORTING
      language              = sy-langu
    IMPORTING
      " Hàm này trả về một bảng (TABLES) chứa tên tất cả các tháng
      " (Cách lấy dữ liệu bảng trong hàm cũ)
    TABLES
      month_names           = DATA(lt_months)
    EXCEPTIONS
      month_names_not_found = 1
      OTHERS                = 2.

  IF sy-subrc = 0.
    " Lấy tháng từ biến uv_date (Định dạng của kiểu 'D' luôn là YYYYMMDD)
    " Ví dụ: 20230725 -> Lấy ký tự vị trí số 4, lấy độ dài 2 ký tự -> '07'
    DATA(lv_month) = uv_date+4(2).
    
    " Đọc bảng tên tháng vừa trả về từ Function Module
    READ TABLE lt_months INTO DATA(ls_month) WITH KEY mnr = lv_month.
    IF sy-subrc = 0.
      lv_month_name = ls_month-ltx.
    ENDIF.
  ENDIF.

  " Format lại chuỗi đầu ra (DD Month YYYY)
  cv_formatted_date = |Ngày { uv_date+6(2) } tháng { lv_month_name } năm { uv_date(4) }|.

ENDFORM.
```

### 🔍 Giải thích Code Chuyên Sâu:
1. `PERFORM ... USING ... CHANGING ...`: Cách gọi một FORM. Biến truyền vào `USING` sẽ đi vào tham số `uv_date`, biến `CHANGING` đi vào `cv_formatted_date`. (Tiền tố `u` = Using, `c` = Changing).
2. `CALL FUNCTION`: SAP đã viết sẵn hàng trăm ngàn Function Modules (T-code `SE37`). Bạn không cần tự lập trình thuật toán tính tên tháng, chỉ cần gọi hàm của SAP và hứng kết quả trả về (`IMPORTING` hoặc `TABLES`).
3. `uv_date+4(2)` (Offset/Length operation): Đây là kỹ thuật cắt chuỗi (Substring) rất quyền năng trong ABAP. `+4` nghĩa là bỏ qua 4 ký tự đầu (YYYY). `(2)` nghĩa là lấy 2 ký tự tiếp theo (MM).
