# 💻 Bài 7.1: Thực Hành Code Giao Diện (Module Pool / Dialog Programming)

Khác với Report (chỉ chạy từ trên xuống dưới 1 lần), Module Pool là kiểu lập trình tương tác (Màn hình cửa sổ). Chương trình sẽ dừng lại chờ người dùng bấm nút, sau đó chạy khối lệnh tương ứng.

### 📝 Đề bài: Viết logic xử lý sự kiện khi người dùng bấm nút "Lưu" (SAVE) hoặc nút "Thoát" (BACK) trên màn hình 0100.

Chương trình Module Pool bắt đầu bằng từ khóa `PROGRAM` thay vì `REPORT`.

```abap
PROGRAM z_dialog_employee.

*----------------------------------------------------------------------*
* 1. Khai báo dữ liệu giao tiếp với Màn hình (Dynpro)
* Tên biến phải đặt trùng với tên Ô Textbox trên giao diện Screen Painter (SE51)
*----------------------------------------------------------------------*
DATA: ok_code TYPE sy-ucomm.  " Biến bắt sự kiện nút bấm

TABLES: zemployee. " Khai báo bảng, các trường như zemployee-emp_name sẽ tự link lên UI

*----------------------------------------------------------------------*
* 2. PBO (Process Before Output) - Sự kiện trước khi màn hình hiện ra
*----------------------------------------------------------------------*
MODULE status_0100 OUTPUT.
  " Cài đặt thanh Menu (PF-STATUS) và Tiêu đề (TITLEBAR)
  SET PF-STATUS 'STATUS_100'.
  SET TITLEBAR 'TITLE_100'.

  " Xử lý logic trước khi show UI: Ví dụ vô hiệu hóa nút bấm nếu không có quyền
  LOOP AT SCREEN.
    IF screen-name = 'ZEMPLOYEE-SALARY' AND sy-uname <> 'HR_MANAGER'.
      screen-input = 0. " Khóa ô Nhập Lương, chỉ cho xem (Read-only)
      MODIFY SCREEN.
    ENDIF.
  ENDLOOP.
ENDMODULE.

*----------------------------------------------------------------------*
* 3. PAI (Process After Input) - Sự kiện sau khi người dùng tương tác
*----------------------------------------------------------------------*
MODULE user_command_0100 INPUT.

  " Gán mã nút bấm mà user vừa bấm vào biến lv_ucomm, sau đó xóa ok_code 
  " (Để tránh mã nút bấm bị dính vào lần chạy tiếp theo)
  DATA(lv_ucomm) = ok_code.
  CLEAR ok_code.

  " Xử lý theo từng nút bấm
  CASE lv_ucomm.
  
    " Nút Thoát / Trở lại
    WHEN 'BACK' OR 'EXIT' OR 'CANC'.
      LEAVE TO SCREEN 0. " Đóng màn hình 0100, quay về màn hình trước đó
      
    " Nút Lưu
    WHEN 'SAVE'.
      " Validate dữ liệu: Không cho phép tên trống
      IF zemployee-emp_name IS INITIAL.
        MESSAGE 'Vui lòng nhập tên nhân viên!' TYPE 'E'. " E = Error, dừng màn hình
      ENDIF.
      
      " Cập nhật vào Database
      MODIFY zemployee. " Lệnh MODIFY sẽ INSERT nếu chưa có, UPDATE nếu đã có
      IF sy-subrc = 0.
        MESSAGE 'Đã lưu thành công!' TYPE 'S'. " S = Success
      ELSE.
        MESSAGE 'Lỗi lưu dữ liệu!' TYPE 'E'.
      ENDIF.
      
    " Nút Xóa
    WHEN 'DELETE'.
      DELETE FROM zemployee WHERE emp_id = zemployee-emp_id.
      MESSAGE 'Đã xóa!' TYPE 'S'.
      CLEAR zemployee. " Làm sạch các ô text trên màn hình

  ENDCASE.

ENDMODULE.
```

### 🔍 Giải thích Code Chuyên Sâu:
1. `MODULE status_0100 OUTPUT`: Khối PBO luôn chạy đầu tiên. Đây là nơi bạn cấu hình UI: Làm mờ ô text, giấu nút bấm, đổ dữ liệu mặc định. Lệnh `LOOP AT SCREEN` chạy qua mọi thành phần UI để bạn chỉnh sửa thuộc tính.
2. `MODULE user_command_0100 INPUT`: Khối PAI chỉ chạy khi User bấm Enter, bấm nút Save, nút Back, hoặc Double Click.
3. `MESSAGE ... TYPE 'E'`: Nếu trong khối PAI bạn quăng ra Message type E (Lỗi), chương trình sẽ khựng lại, báo đỏ ở góc dưới màn hình và BẮT BUỘC người dùng nhập lại ô bị lỗi, không cho chạy tiếp xuống dưới.
