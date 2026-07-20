# 💻 Bài 3.1: Thực Hành Code Internal Tables & Open SQL

Internal Table là linh hồn của ABAP. Mọi thao tác xử lý dữ liệu số lượng lớn đều diễn ra trên RAM thông qua Internal Table.

### 📝 Đề bài: Lấy danh sách Vật tư từ Database, xử lý trên RAM và in ra.

```abap
REPORT z_internal_table_demo.

*----------------------------------------------------------------------*
* 1. Khai báo Data Dictionary & Internal Tables
*----------------------------------------------------------------------*
" 1.1 Khai báo cấu trúc (Structure) chứa các cột mong muốn
TYPES: BEGIN OF ty_material,
         matnr TYPE mara-matnr, " Mã vật tư
         mtart TYPE mara-mtart, " Loại vật tư
         maktx TYPE makt-maktx, " Tên vật tư
       END OF ty_material.

" 1.2 Khai báo Internal Table (Mảng chứa dữ liệu)
DATA: gt_mara_makt TYPE TABLE OF ty_material, " Bảng chứa kết quả chuẩn
      gt_mara      TYPE TABLE OF mara.        " Bảng tạm lấy từ DB

*----------------------------------------------------------------------*
* 2. Lấy dữ liệu từ Database (Open SQL)
*----------------------------------------------------------------------*
START-OF-SELECTION.

  " Lấy 100 vật tư loại 'FERT' (Thành phẩm) từ bảng MARA đưa vào mảng gt_mara
  SELECT * FROM mara 
    INTO TABLE @gt_mara 
    UP TO 100 ROWS
    WHERE mtart = 'FERT'.

  " Kiểm tra xem có lấy được dữ liệu không?
  IF sy-subrc <> 0 OR gt_mara IS INITIAL.
    WRITE: / 'Không có dữ liệu!'.
    RETURN.
  ENDIF.

  " Dùng FOR ALL ENTRIES để lấy tên vật tư tương ứng từ bảng MAKT
  " (Tránh dùng SELECT bên trong vòng LOOP)
  SELECT matnr, maktx FROM makt 
    INTO TABLE @DATA(lt_makt) " Khai báo Inline Internal Table (Mới)
    FOR ALL ENTRIES IN @gt_mara
    WHERE matnr = @gt_mara-matnr
      AND spras = @sy-langu. " Chỉ lấy ngôn ngữ hiện tại đang đăng nhập

  " Sắp xếp bảng lt_makt để tối ưu hóa lệnh đọc BINARY SEARCH
  SORT lt_makt BY matnr.

*----------------------------------------------------------------------*
* 3. Xử lý Internal Table trên RAM
*----------------------------------------------------------------------*
  " Duyệt qua bảng gt_mara bằng FIELD-SYMBOL (Nhanh hơn dùng Work Area)
  LOOP AT gt_mara ASSIGNING FIELD-SYMBOL(<fs_mara>).
    
    " Khai báo biến tạm chứa 1 dòng kết quả
    DATA(ls_result) = VALUE ty_material( 
                        matnr = <fs_mara>-matnr 
                        mtart = <fs_mara>-mtart ).

    " Đọc bảng tên vật tư (Sử dụng BINARY SEARCH để tốc độ cực nhanh)
    READ TABLE lt_makt INTO DATA(ls_makt) 
         WITH KEY matnr = <fs_mara>-matnr 
         BINARY SEARCH.
         
    IF sy-subrc = 0.
      ls_result-maktx = ls_makt-maktx.
    ELSE.
      ls_result-maktx = 'Không có tên'.
    ENDIF.

    " Thêm dòng vừa xử lý vào bảng kết quả cuối cùng
    APPEND ls_result TO gt_mara_makt.
    
  ENDLOOP.

*----------------------------------------------------------------------*
* 4. Hiển thị kết quả
*----------------------------------------------------------------------*
  WRITE: / 'DANH SÁCH VẬT TƯ THÀNH PHẨM (FERT)'.
  ULINE.
  
  LOOP AT gt_mara_makt INTO DATA(ls_display).
    WRITE: / ls_display-matnr, '|', ls_display-mtart, '|', ls_display-maktx.
  ENDLOOP.
```

### 🔍 Giải thích Code Chuyên Sâu:
1. `UP TO 100 ROWS`: Luôn dùng lệnh này khi test trên môi trường Dev để tránh tải hàng triệu dòng làm treo hệ thống.
2. `FOR ALL ENTRIES IN`: Kỹ thuật gom một danh sách các mã (ví dụ 100 mã vật tư) và bắn xuống Database một lần duy nhất để lấy tên, thay vì bắn xuống 100 lần.
3. `SORT ... BY ...` kết hợp `BINARY SEARCH`: Bắt buộc phải có SORT trước khi dùng BINARY SEARCH. Thuật toán này chia đôi mảng để tìm kiếm. Với bảng 1 triệu dòng, Linear Search mất 1 triệu lần tìm, Binary Search chỉ mất 20 lần!
4. `FIELD-SYMBOL(<fs>)`: Là con trỏ bộ nhớ (Pointer). Dùng cái này khi LOOP sẽ không làm phát sinh thao tác copy dữ liệu vật lý (từ Internal table sang Work Area), qua đó tiết kiệm RAM và CPU.
