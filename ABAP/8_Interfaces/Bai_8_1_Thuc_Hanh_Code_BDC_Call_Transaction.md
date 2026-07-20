# 💻 Bài 8.1: Thực Hành Code Đẩy Dữ Liệu Tự Động Bằng BDC

BDC (Batch Data Communication) là kỹ thuật kinh điển để import dữ liệu từ file Excel (hoặc hệ thống khác) vào màn hình SAP tự động như có một con robot đang gõ phím.

### 📝 Đề bài: Viết chương trình cập nhật tên vật tư vào màn hình T-Code `MM02`.

```abap
REPORT z_bdc_update_material.

*----------------------------------------------------------------------*
* 1. Khai báo Bảng BDC (Bảng chứa kịch bản quay màn hình)
*----------------------------------------------------------------------*
DATA: gt_bdcdata TYPE TABLE OF bdcdata,  " Bảng chứa các nút bấm, ô nhập liệu
      gs_bdcdata TYPE bdcdata,
      gt_msg     TYPE TABLE OF bdcmsgcoll, " Bảng chứa kết quả trả về (Lỗi, Thành công)
      gs_msg     TYPE bdcmsgcoll.

* Khai báo biến
DATA: lv_material TYPE mara-matnr VALUE '1000',
      lv_new_desc TYPE makt-maktx VALUE 'Tên vật tư đã được sửa tự động'.

*----------------------------------------------------------------------*
* 2. Xây dựng Kịch bản (Populate BDC Data)
* Kịch bản này có được nhờ việc bạn dùng T-Code SHDB ghi hình lại (Record)
*----------------------------------------------------------------------*
START-OF-SELECTION.

  CLEAR gt_bdcdata.

  " --- Màn hình 1: Màn hình nhập mã vật tư (Screen 0060 của SAPLMGMM)
  gs_bdcdata-program  = 'SAPLMGMM'.
  gs_bdcdata-dynpro   = '0060'.
  gs_bdcdata-dynbegin = 'X'.       " Đánh dấu bắt đầu một màn hình mới
  APPEND gs_bdcdata TO gt_bdcdata. CLEAR gs_bdcdata.

  " Điền mã vật tư vào ô textbox
  gs_bdcdata-fnam     = 'RMMG1-MATNR'.
  gs_bdcdata-fval     = lv_material.
  APPEND gs_bdcdata TO gt_bdcdata. CLEAR gs_bdcdata.

  " Bấm nút Enter (Mã chức năng = /00)
  gs_bdcdata-fnam     = 'BDC_OKCODE'.
  gs_bdcdata-fval     = '/00'.
  APPEND gs_bdcdata TO gt_bdcdata. CLEAR gs_bdcdata.

  " --- Màn hình 2: Bảng chọn View (Select View - Screen 0070)
  gs_bdcdata-program  = 'SAPLMGMM'.
  gs_bdcdata-dynpro   = '0070'.
  gs_bdcdata-dynbegin = 'X'.
  APPEND gs_bdcdata TO gt_bdcdata. CLEAR gs_bdcdata.

  " Chọn dòng Basic Data 1 và bấm Enter
  gs_bdcdata-fnam     = 'MSICHTAUSW-KZSEL(01)'.
  gs_bdcdata-fval     = 'X'.
  APPEND gs_bdcdata TO gt_bdcdata. CLEAR gs_bdcdata.

  gs_bdcdata-fnam     = 'BDC_OKCODE'.
  gs_bdcdata-fval     = '=ENTR'. " Tùy hệ thống nút Enter có thể là /00 hoặc =ENTR
  APPEND gs_bdcdata TO gt_bdcdata. CLEAR gs_bdcdata.

  " --- Màn hình 3: Sửa mô tả (Screen 4004)
  gs_bdcdata-program  = 'SAPLMGMM'.
  gs_bdcdata-dynpro   = '4004'.
  gs_bdcdata-dynbegin = 'X'.
  APPEND gs_bdcdata TO gt_bdcdata. CLEAR gs_bdcdata.

  " Ghi đè tên vật tư mới
  gs_bdcdata-fnam     = 'MAKT-MAKTX'.
  gs_bdcdata-fval     = lv_new_desc.
  APPEND gs_bdcdata TO gt_bdcdata. CLEAR gs_bdcdata.

  " Bấm nút Save (Lưu)
  gs_bdcdata-fnam     = 'BDC_OKCODE'.
  gs_bdcdata-fval     = '=BU'.
  APPEND gs_bdcdata TO gt_bdcdata. CLEAR gs_bdcdata.

*----------------------------------------------------------------------*
* 3. Kích Hoạt Robot Đóng Vai Người Dùng (CALL TRANSACTION)
*----------------------------------------------------------------------*
  " Gọi T-Code MM02 và nhồi bảng kịch bản bdcdata vào
  " MODE 'N': Chạy ngầm (No screen), người dùng không thấy màn hình giật giật.
  " UPDATE 'S': Synchronous, chờ lưu xong DB mới chạy tiếp.
  
  CALL TRANSACTION 'MM02' USING gt_bdcdata
                          MODE   'N'
                          UPDATE 'S'
                          MESSAGES INTO gt_msg.

  " Phân tích kết quả trả về
  IF sy-subrc = 0.
    WRITE: / 'Thành công! Vật tư', lv_material, 'đã được sửa tên.'.
  ELSE.
    WRITE: / 'Có lỗi xảy ra:'.
    " Lấy ra thông báo lỗi chi tiết từ bảng gt_msg
    LOOP AT gt_msg INTO gs_msg WHERE msgtyp = 'E'. " E = Error
      " Hàm này dịch mã lỗi (ví dụ: M3 021) thành chữ (ví dụ: Vật tư đang bị khóa)
      CALL FUNCTION 'MESSAGE_TEXT_BUILD'
        EXPORTING
          msgid               = gs_msg-msgid
          msgnr               = gs_msg-msgnr
          msgv1               = gs_msg-msgv1
          msgv2               = gs_msg-msgv2
          msgv3               = gs_msg-msgv3
          msgv4               = gs_msg-msgv4
        IMPORTING
          message_text_output = DATA(lv_error_text).
          
      WRITE: / '-', lv_error_text COLOR COL_NEGATIVE.
    ENDLOOP.
  ENDIF.
```

### 🔍 Giải thích Code Chuyên Sâu:
1. `bdcdata`: Một Structure chuẩn của SAP gồm các trường: Tên Program, Số màn hình (Dynpro), Tên field (FNAM) và Giá trị field (FVAL).
2. Kỹ thuật này giả lập y hệt việc bạn di con trỏ chuột bấm vào màn hình. Do đó, nếu màn hình SAP thay đổi giao diện (thêm cột mới, đổi số hiệu màn hình), code BDC này sẽ báo lỗi.
3. Trong thực tế, bạn sẽ dùng hàm `ALSM_EXCEL_TO_INTERNAL_TABLE` đọc một file Excel chứa 1000 mã vật tư -> LOOP qua bảng Excel -> Và thả toàn bộ khối code điền `bdcdata` này vào trong vòng LOOP để nó chạy cập nhật tự động 1000 vật tư trong nháy mắt.
