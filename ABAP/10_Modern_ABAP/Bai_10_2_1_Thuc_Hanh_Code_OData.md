# 💻 Bài 10.2.1: Thực Hành Code OData Service (SEGW)

Trong SEGW, sau khi bạn định nghĩa Data Model và bấm Generate, SAP sinh ra class kết thúc bằng `_DPC_EXT`. Nhiệm vụ của lập trình viên là mở class này lên (T-code SE24) và viết code ABAP đè lên (Redefine) các hàm chuẩn.

Dưới đây là mã nguồn cho các hàm kinh điển của OData (CRUD).

---

### 1. Hàm đọc danh sách: `MATERIALSET_GET_ENTITYSET`
Hàm này tương đương lệnh **GET** trả về một List (Ví dụ khi Fiori Load dữ liệu lên Bảng).

```abap
  METHOD materialset_get_entityset.
    
    DATA: lt_mara TYPE TABLE OF mara,
          ls_entity LIKE LINE OF et_entityset. " et_entityset là tham số đầu ra chứa kết quả

    " Lấy tham số Paging (Phân trang) từ Frontend gửi lên: $top và $skip
    DATA(lv_top)  = io_tech_request_context->get_top( ).
    DATA(lv_skip) = io_tech_request_context->get_skip( ).

    " Truy vấn DB (Lấy top 100 dòng làm ví dụ)
    SELECT * FROM mara INTO TABLE @lt_mara UP TO 100 ROWS.

    " Đổ dữ liệu từ bảng DB sang bảng kết quả của OData
    LOOP AT lt_mara INTO DATA(ls_mara).
      ls_entity-matnr = ls_mara-matnr.
      ls_entity-mtart = ls_mara-mtart.
      ls_entity-ersda = ls_mara-ersda.
      APPEND ls_entity TO et_entityset.
    ENDLOOP.

  ENDMETHOD.
```

---

### 2. Hàm đọc 1 dòng chi tiết: `MATERIALSET_GET_ENTITY`
Hàm này tương đương lệnh **GET** nhưng truyền theo Key (Ví dụ click vào 1 dòng trên bảng để xem chi tiết `MATERIALSET('1000')`).

```abap
  METHOD materialset_get_entity.
  
    DATA: ls_key LIKE LINE OF it_key_tab, " Bảng chứa các key mà Frontend gửi lên
          lv_matnr TYPE mara-matnr.

    " 1. Đọc key truyền vào từ URL
    READ TABLE it_key_tab INTO ls_key WITH KEY name = 'Matnr'.
    IF sy-subrc = 0.
      lv_matnr = ls_key-value.
      
      " Format lại số 0 ở đầu nếu cần
      CALL FUNCTION 'CONVERSION_EXIT_MATN1_INPUT'
        EXPORTING
          input  = lv_matnr
        IMPORTING
          output = lv_matnr.
          
      " 2. Truy vấn DB
      SELECT SINGLE * FROM mara INTO @DATA(ls_mara) WHERE matnr = @lv_matnr.
      
      " 3. Trả dữ liệu ra cho Frontend (er_entity)
      IF sy-subrc = 0.
        er_entity-matnr = ls_mara-matnr.
        er_entity-mtart = ls_mara-mtart.
        er_entity-ersda = ls_mara-ersda.
      ELSE.
        " Ném lỗi 404 Not Found nếu không có
        RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
          EXPORTING
            textid = /iwbep/cx_mgw_busi_exception=>business_error
            message = 'Không tìm thấy vật tư này!'.
      ENDIF.
    ENDIF.

  ENDMETHOD.
```

---

### 3. Hàm tạo mới dữ liệu: `MATERIALSET_CREATE_ENTITY`
Tương đương lệnh **POST**. Frontend gửi một cục JSON lên, backend đọc và Insert vào bảng.

```abap
  METHOD materialset_create_entity.
  
    DATA: ls_request TYPE zcl_z_my_odata_mpc=>ts_material. " Cấu trúc dữ liệu của OData
    
    " 1. Đọc chuỗi JSON mà Frontend gửi lên và chuyển thành ABAP Structure
    io_data_provider->read_entry_data( IMPORTING es_data = ls_request ).
    
    " 2. Validate dữ liệu
    IF ls_request-matnr IS INITIAL.
      RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
        EXPORTING
          message = 'Mã vật tư không được để trống!'.
    ENDIF.

    " 3. Ghi vào bảng Z của bạn (Ví dụ)
    DATA(ls_zmara) = VALUE zmara( matnr = ls_request-matnr
                                  mtart = ls_request-mtart ).
    INSERT zmara FROM ls_zmara.
    
    IF sy-subrc = 0.
      " 4. Trả ngược lại cấu trúc đã tạo thành công cho Frontend biết
      er_entity = ls_request.
    ELSE.
      RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
        EXPORTING
          message = 'Tạo vật tư thất bại (Lỗi DB)'.
    ENDIF.

  ENDMETHOD.
```
