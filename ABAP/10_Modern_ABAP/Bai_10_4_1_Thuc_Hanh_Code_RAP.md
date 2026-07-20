# 💻 Bài 10.4.1: Thực Hành Code ABAP RAP (RESTful Application Programming)

ABAP RAP là phương thức lập trình hiện đại nhất trên SAP S/4HANA. Mọi thứ được định nghĩa qua CDS Views và tự động sinh ra OData mà không cần dùng SEGW.

Dưới đây là 3 bước cốt lõi để tạo ra một RAP Model cho chức năng Quản lý Đơn hàng (Travel Booking). Mọi code này được viết trên **Eclipse ADT**.

---

### Bước 1: Data Model (Tạo CDS View cho dữ liệu - Thay thế SE11)
Bạn viết code CDS để tạo View (Đọc từ bảng DB `ztravel`).
File: `ZI_TRAVEL_M.asddls`

```abap
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Travel Data Model'
define root view entity ZI_TRAVEL_M
  as select from ztravel as Travel
{
  key travel_id       as TravelID,
      agency_id       as AgencyID,
      customer_id     as CustomerID,
      begin_date      as BeginDate,
      end_date        as EndDate,
      @Semantics.amount.currencyCode: 'CurrencyCode'
      booking_fee     as BookingFee,
      currency_code   as CurrencyCode,
      status          as TravelStatus,
      
      // Khai báo quản lý thông tin hệ thống tự sinh
      @Semantics.user.createdBy: true
      created_by      as CreatedBy,
      @Semantics.systemDateTime.createdAt: true
      created_at      as CreatedAt
}
```

---

### Bước 2: Behavior Definition (Định nghĩa Hành vi)
Bạn muốn làm gì với cái View trên? Bạn định nghĩa vào đây (Create, Update, hay xóa?).
File: `ZI_TRAVEL_M.asbdef`

```abap
managed implementation in class zbp_i_travel_m unique;

define behavior for ZI_TRAVEL_M alias Travel
persistent table ztravel  // Lưu dữ liệu vào bảng vật lý nào?
lock master               // Khóa dữ liệu tự động
authorization master ( instance )
etag master CreatedAt     // Chống ghi đè nếu dữ liệu bị cũ
{
  // Khai báo SAP TỰ ĐỘNG xử lý 3 hàm cơ bản này (Bạn KHÔNG CẦN viết code ABAP!)
  create;
  update;
  delete;

  // Khai báo một nút bấm Custom (Ví dụ: Nút "Chấp nhận Đơn hàng")
  action AcceptTravel result [1] $self;

  // Gắn các trường chỉ đọc (Read Only - Không cho sửa từ frontend)
  field ( readonly ) TravelID, CreatedBy, CreatedAt;
  
  // Tự động sinh ID khi tạo mới
  field ( numbering : managed ) TravelID;
}
```

---

### Bước 3: Behavior Implementation (Viết logic Custom bằng ABAP)
Vì hàm Create, Update, Delete đã được SAP tự lo nhờ từ khóa `managed`, ta chỉ phải viết code cho cái nút Custom `AcceptTravel` ở trên.
File Class: `ZBP_I_TRAVEL_M`

```abap
CLASS lhc_Travel DEFINITION INHERITING FROM cl_abap_behavior_handler.
  PRIVATE SECTION.
    METHODS accept_travel FOR MODIFY
      IMPORTING keys FOR ACTION Travel~AcceptTravel RESULT result.
ENDCLASS.

CLASS lhc_Travel IMPLEMENTATION.

  METHOD accept_travel.
    " 1. Đổi trạng thái 'status' thành 'A' (Accepted) cho các travel_id được gửi lên
    MODIFY ENTITIES OF zi_travel_m IN LOCAL MODE
      ENTITY Travel
         UPDATE
           FIELDS ( TravelStatus )
           WITH VALUE #( FOR key IN keys
                           ( %tky         = key-%tky
                             TravelStatus = 'A' ) )
      FAILED failed
      REPORTED reported.

    " 2. Trả kết quả về cho Frontend để cập nhật lại giao diện
    READ ENTITIES OF zi_travel_m IN LOCAL MODE
      ENTITY Travel
        ALL FIELDS WITH CORRESPONDING #( keys )
      RESULT DATA(travels).

    result = VALUE #( FOR travel IN travels
                        ( %tky   = travel-%tky
                          %param = travel ) ).
  ENDMETHOD.

ENDCLASS.
```

### 🔍 Sự Khác Biệt Giữa RAP và Kiểu Cũ (SEGW):
1. Không còn những file DPC_EXT dài cả ngàn dòng với toàn lệnh `SELECT` và `INSERT`. Khung RAP (RAP Framework) làm tất cả cho bạn nhờ từ khóa `managed`.
2. ABAP Entity Manipulation Language (**EML**): Bạn thấy cú pháp `MODIFY ENTITIES OF...` ở trên không? Nó là ngôn ngữ riêng của RAP để cập nhật dữ liệu, thay thế cho lệnh `UPDATE` hay `INSERT` truyền thống, tích hợp sâu với quy trình quản lý Transaction (Commit/Rollback).
