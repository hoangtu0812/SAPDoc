# 💻 Bài 10.5: Thực Hành Code REST API Bằng ABAP (SICF)

**Mục tiêu:** Viết một REST API từ con số 0 trong SAP ECC/S4HANA, trả về định dạng JSON và test bằng Postman.

---

## 1. Yêu cầu bài toán
Viết một API có phương thức **GET**. Khi truyền mã Vật tư (`MATNR`) vào URL, SAP sẽ query bảng `MARA` và trả về thông tin Vật tư đó dưới dạng JSON.

---

## 2. Bước 1: Tạo Class Xử Lý (Handler Class)
Vào T-Code **`SE24`**, tạo một class tên là `ZCL_REST_MATERIAL_HANDLER`.

1. Chuyển sang tab **Interfaces**, khai báo Interface bắt buộc: **`IF_HTTP_EXTENSION`**.
2. Nhấn đúp vào method `IF_HTTP_EXTENSION~HANDLE_REQUEST` để viết code.

### 📜 Code mẫu cho method HANDLE_REQUEST:
```abap
  METHOD if_http_extension~handle_request.
    " 1. Khai báo biến
    DATA: lv_matnr TYPE matnr,
          ls_mara  TYPE mara,
          lv_json  TYPE string.

    " 2. Lấy tham số 'matnr' từ URL (Ví dụ: ?matnr=1000)
    lv_matnr = server->request->get_form_field( 'matnr' ).

    " 3. Query dữ liệu từ Database
    IF lv_matnr IS NOT INITIAL.
      " Format lại mã vật tư cho chuẩn SAP (Thêm số 0 ở đầu nếu cần)
      CALL FUNCTION 'CONVERSION_EXIT_MATN1_INPUT'
        EXPORTING
          input  = lv_matnr
        IMPORTING
          output = lv_matnr.

      SELECT SINGLE * FROM mara INTO ls_mara WHERE matnr = lv_matnr.

      IF sy-subrc = 0.
        " 4. Chuyển đổi dữ liệu ABAP (ls_mara) thành JSON
        lv_json = /ui2/cl_json=>serialize( data = ls_mara ).
        
        " 5. Trả về JSON cho Client
        server->response->set_cdata( lv_json ).
        server->response->set_header_field( name = 'Content-Type' value = 'application/json' ).
        server->response->set_status( code = 200 reason = 'OK' ).
      ELSE.
        " Không tìm thấy vật tư
        server->response->set_cdata( '{"error": "Material not found"}' ).
        server->response->set_header_field( name = 'Content-Type' value = 'application/json' ).
        server->response->set_status( code = 404 reason = 'Not Found' ).
      ENDIF.
    ELSE.
      " Lỗi thiếu tham số
      server->response->set_cdata( '{"error": "Missing parameter matnr"}' ).
      server->response->set_header_field( name = 'Content-Type' value = 'application/json' ).
      server->response->set_status( code = 400 reason = 'Bad Request' ).
    ENDIF.
  ENDMETHOD.
```

---

## 3. Bước 2: Đăng ký URL trong SICF
Class vừa viết xong chưa thể gọi được từ bên ngoài. Ta cần tạo một cái "cổng" (URL Endpoint).

1. Vào T-Code **`SICF`**.
2. Đi theo đường dẫn: `default_host -> sap -> bc`.
3. Chuột phải vào thư mục `bc`, chọn **New Sub-Element**.
4. Nhập tên là `z_material_api` (Đây sẽ là một phần của đường dẫn URL).
5. Mở màn hình cấu hình, chuyển sang tab **Handler List**.
6. Điền tên class vừa tạo: `ZCL_REST_MATERIAL_HANDLER` vào dòng số 1.
7. Lưu lại. Chuột phải vào node `z_material_api` vừa tạo và chọn **Activate Service**.

---

## 4. Bước 3: Kiểm thử bằng Postman
Bây giờ, API của bạn đã sẵn sàng.

1. Lấy URL gốc của SAP (Có thể xem bằng T-code `SMICM` -> Goto -> Services). Ví dụ: `http://saperp.company.com:8000`.
2. URL API của bạn sẽ là: `http://saperp.company.com:8000/sap/bc/z_material_api?matnr=1000`
3. Mở phần mềm **Postman**:
   * Phương thức: **GET**
   * Nhập URL trên.
   * Chuyển sang tab **Authorization**, chọn **Basic Auth**, nhập Username/Password của SAP.
   * Nhấn **Send**.

### Kết quả mong đợi:
Postman sẽ trả về Status 200 OK cùng đoạn JSON:
```json
{
    "MANDT": "100",
    "MATNR": "000000000000001000",
    "ERSDA": "2023-01-01",
    "ERNAM": "SAPUSER",
    "MTART": "FERT",
    "MBRSH": "M",
    "MEINS": "PC"
}
```
Chúc mừng! Bạn đã tự tay viết và cấu hình thành công một REST API trên SAP.
