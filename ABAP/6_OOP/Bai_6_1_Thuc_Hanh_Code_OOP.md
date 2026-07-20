# 💻 Bài 6.1: Thực Hành Code Hướng Đối Tượng (ABAP OOP)

ABAP Objects (OOP) là bắt buộc đối với SAP S/4HANA và Fiori. Trong thực tế, class thường được tạo độc lập bằng T-Code `SE24` (Global Class). Tuy nhiên để dễ hình dung, dưới đây là một Local Class tạo ngay trong `SE38`.

### 📝 Đề bài: Xây dựng class "Tài khoản ngân hàng" với các hàm Rút tiền, Gửi tiền, và ném lỗi (Exception) khi hết tiền.

```abap
REPORT z_oop_bank_account.

*----------------------------------------------------------------------*
* 1. Khai báo Lớp Xử lý Lỗi (Exception Class)
*----------------------------------------------------------------------*
" Định nghĩa một Exception tùy chỉnh để quăng lỗi khi số tiền không đủ
CLASS lcx_insufficient_funds DEFINITION INHERITING FROM cx_static_check.
ENDCLASS.

*----------------------------------------------------------------------*
* 2. Khai báo Lớp (Class Definition) - Đóng vai trò như bản thiết kế
*----------------------------------------------------------------------*
CLASS lcl_bank_account DEFINITION.
  PUBLIC SECTION.
    " Các phương thức và thuộc tính public mà bên ngoài gọi được
    METHODS constructor
      IMPORTING iv_account_no TYPE string
                iv_balance    TYPE p.
                
    METHODS deposit
      IMPORTING iv_amount TYPE p.
      
    METHODS withdraw
      IMPORTING iv_amount TYPE p
      RAISING   lcx_insufficient_funds. " Khai báo hàm này có khả năng ném ra lỗi
      
    METHODS get_balance
      RETURNING VALUE(rv_balance) TYPE p.

  PRIVATE SECTION.
    " Dữ liệu nhạy cảm, chỉ được sửa chữa thông qua các method bên trên
    DATA: mv_account_no TYPE string,
          mv_balance    TYPE p DECIMALS 2.
ENDCLASS.

*----------------------------------------------------------------------*
* 3. Triển khai Lớp (Class Implementation) - Nơi viết logic thực sự
*----------------------------------------------------------------------*
CLASS lcl_bank_account IMPLEMENTATION.
  
  " Constructor là hàm chạy tự động ngay khi khởi tạo Object (CREATE OBJECT)
  METHOD constructor.
    mv_account_no = iv_account_no.
    mv_balance    = iv_balance.
  ENDMETHOD.

  METHOD deposit.
    mv_balance = mv_balance + iv_amount.
  ENDMETHOD.

  METHOD withdraw.
    IF iv_amount > mv_balance.
      " Lỗi! Số tiền rút lớn hơn số dư -> Quăng Exception ra ngoài
      RAISE EXCEPTION TYPE lcx_insufficient_funds.
    ELSE.
      mv_balance = mv_balance - iv_amount.
    ENDIF.
  ENDMETHOD.

  METHOD get_balance.
    rv_balance = mv_balance.
  ENDMETHOD.

ENDCLASS.

*----------------------------------------------------------------------*
* 4. Chương trình chính (Thực thi)
*----------------------------------------------------------------------*
START-OF-SELECTION.

  " Khai báo con trỏ đối tượng
  DATA: lo_my_bank TYPE REF TO lcl_bank_account,
        lv_balance TYPE p DECIMALS 2.

  " Khởi tạo đối tượng (Cấp phát vào RAM) với số dư ban đầu 1,000,000
  CREATE OBJECT lo_my_bank
    EXPORTING
      iv_account_no = '0123456789'
      iv_balance    = 1000000.

  " Gọi phương thức nạp tiền (Gửi thêm 500,000)
  lo_my_bank->deposit( 500000 ).

  " Khối TRY...CATCH để bắt lỗi an toàn, chống Dump
  TRY.
      " Rút 2,000,000 (Sẽ gây lỗi vì số dư hiện tại chỉ có 1,500,000)
      lo_my_bank->withdraw( 2000000 ).
      WRITE: / 'Rút tiền thành công!'.
      
    CATCH lcx_insufficient_funds.
      " Bắt lỗi ở đây và in câu thông báo thân thiện
      WRITE: / 'LỖI: Số dư tài khoản không đủ để thực hiện giao dịch!' COLOR COL_NEGATIVE.
  ENDTRY.

  " Kiểm tra số dư cuối cùng
  lv_balance = lo_my_bank->get_balance( ).
  WRITE: / 'Số dư hiện tại:', lv_balance.
```

### 🔍 Giải thích Code Chuyên Sâu:
1. `PRIVATE SECTION`: Biến `mv_balance` được che giấu (Encapsulation). Không một đoạn code bên ngoài nào có thể gọi lệnh `lo_my_bank->mv_balance = 9999999` để gian lận số dư. Bắt buộc phải thông qua hàm `deposit` và `withdraw`.
2. `RAISING lcx_insufficient_funds`: Đây là cách báo hiệu cho lập trình viên khác biết: "Hàm này nguy hiểm, có thể xảy ra lỗi, hãy cẩn thận bọc nó trong `TRY...CATCH` đi!".
3. `TRY...CATCH`: Khối lệnh kinh điển. Nếu chạy hàm `withdraw` mà có lệnh `RAISE EXCEPTION` phát ra, hệ thống lập tức nhảy từ dòng `lo_my_bank->withdraw` xuống thẳng khối `CATCH` và bỏ qua câu chữ "Rút tiền thành công". Hệ thống không bị treo (Dump).
