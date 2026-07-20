# 💻 Bài 5.1: Thực Hành Code ALV Grid Report (Cơ Bản)

Dưới đây là khung code mẫu (Template) chuẩn nhất để viết một báo cáo ALV lấy danh sách Đơn hàng bán (VBAK & VBAP). Bạn có thể copy đoạn code này và chạy trực tiếp trong `SE38`.

```abap
REPORT z_alv_sales_order.

*----------------------------------------------------------------------*
* 1. Data Declaration (Khai báo dữ liệu)
*----------------------------------------------------------------------*
" Định nghĩa cấu trúc cho báo cáo
TYPES: BEGIN OF ty_report,
         vbeln TYPE vbak-vbeln, " Mã đơn hàng
         erdat TYPE vbak-erdat, " Ngày tạo
         kunnr TYPE vbak-kunnr, " Khách hàng
         posnr TYPE vbap-posnr, " Mã dòng
         matnr TYPE vbap-matnr, " Mã vật tư
         kwmeng TYPE vbap-kwmeng, " Số lượng
       END OF ty_report.

" Khai báo Internal Table và Work Area
DATA: gt_report TYPE TABLE OF ty_report,
      gs_report TYPE ty_report.

" Dữ liệu cho ALV
DATA: gt_fieldcat TYPE slis_t_fieldcat_alv,
      gs_layout   TYPE slis_layout_alv.

*----------------------------------------------------------------------*
* 2. Selection Screen (Màn hình chọn lọc)
*----------------------------------------------------------------------*
" Khai báo biến dummy để tham chiếu tìm kiếm F4
TABLES: vbak. 

SELECTION-SCREEN BEGIN OF BLOCK b1 WITH FRAME TITLE TEXT-001.
  SELECT-OPTIONS: s_vbeln FOR vbak-vbeln, " Lọc theo mã Đơn hàng
                  s_erdat FOR vbak-erdat. " Lọc theo ngày tạo
SELECTION-SCREEN END OF BLOCK b1.

*----------------------------------------------------------------------*
* 3. Main Program (Logic chính)
*----------------------------------------------------------------------*
START-OF-SELECTION.
  PERFORM get_data.
  IF gt_report IS NOT INITIAL.
    PERFORM build_fieldcat.
    PERFORM display_alv.
  ELSE.
    MESSAGE 'Không tìm thấy dữ liệu!' TYPE 'I'.
  ENDIF.

*----------------------------------------------------------------------*
* 4. Subroutines (Các hàm con)
*----------------------------------------------------------------------*
FORM get_data.
  " Sử dụng JOIN để lấy Header (VBAK) và Item (VBAP)
  SELECT a~vbeln, a~erdat, a~kunnr, b~posnr, b~matnr, b~kwmeng
    INTO TABLE @gt_report
    FROM vbak AS a
    INNER JOIN vbap AS b ON a~vbeln = b~vbeln
    WHERE a~vbeln IN @s_vbeln
      AND a~erdat IN @s_erdat.
ENDFORM.

FORM build_fieldcat.
  " Cột 1: Mã đơn hàng
  APPEND INITIAL LINE TO gt_fieldcat ASSIGNING FIELD-SYMBOL(<fc>).
  <fc>-fieldname = 'VBELN'.
  <fc>-seltext_m = 'Sales Order'.
  <fc>-key       = 'X'. " Khóa chính, sẽ có màu nổi bật

  " Cột 2: Ngày tạo
  APPEND INITIAL LINE TO gt_fieldcat ASSIGNING <fc>.
  <fc>-fieldname = 'ERDAT'.
  <fc>-seltext_m = 'Created On'.

  " Cột 3: Khách hàng
  APPEND INITIAL LINE TO gt_fieldcat ASSIGNING <fc>.
  <fc>-fieldname = 'KUNNR'.
  <fc>-seltext_m = 'Customer'.

  " Cột 4: Mã dòng
  APPEND INITIAL LINE TO gt_fieldcat ASSIGNING <fc>.
  <fc>-fieldname = 'POSNR'.
  <fc>-seltext_m = 'Item No'.

  " Cột 5: Mã vật tư
  APPEND INITIAL LINE TO gt_fieldcat ASSIGNING <fc>.
  <fc>-fieldname = 'MATNR'.
  <fc>-seltext_m = 'Material'.

  " Cột 6: Số lượng
  APPEND INITIAL LINE TO gt_fieldcat ASSIGNING <fc>.
  <fc>-fieldname = 'KWMENG'.
  <fc>-seltext_m = 'Quantity'.
  <fc>-do_sum    = 'X'. " Tự động tính tổng cho cột này
ENDFORM.

FORM display_alv.
  " Tùy chỉnh Layout cho ALV
  gs_layout-colwidth_optimize = 'X'. " Tự động dãn cột cho vừa chữ
  gs_layout-zebra             = 'X'. " Hiển thị màu kẻ sọc ngựa vằn

  " Gọi Function Module vẽ ALV
  CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY'
    EXPORTING
      is_layout   = gs_layout
      it_fieldcat = gt_fieldcat
    TABLES
      t_outtab    = gt_report
    EXCEPTIONS
      program_error = 1
      OTHERS        = 2.
ENDFORM.
```
