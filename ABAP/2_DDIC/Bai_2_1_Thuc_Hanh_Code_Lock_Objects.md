# 💻 Bài 2.1: Thực Hành Lập Trình Với Data Dictionary (Khóa Dữ Liệu - Lock Objects)

Mặc dù Data Dictionary (SE11) chủ yếu là tạo Bảng, View bằng giao diện chuột, nhưng có một khái niệm cực kỳ quan trọng đòi hỏi phải viết code: **Lock Objects (Cơ chế Khóa)**.

### 📝 Đề bài: Viết chương trình cập nhật số điện thoại của Nhân viên. Đảm bảo rằng nếu có 2 người cùng chạy chương trình này để sửa 1 nhân viên thì người thứ 2 sẽ bị chặn (Tránh đụng độ dữ liệu).

Giả sử ta đã tạo một Lock Object trong SE11 tên là `EZ_EMPLOYEE` dựa trên bảng `ZEMPLOYEE`. Hệ thống tự sinh ra 2 hàm: `ENQUEUE_EZ_EMPLOYEE` (Khóa) và `DEQUEUE_EZ_EMPLOYEE` (Mở khóa).

```abap
REPORT z_ddic_lock_demo.

PARAMETERS: p_emp_id TYPE zemployee-emp_id,  " Mã nhân viên
            p_phone  TYPE zemployee-phone.   " Số điện thoại mới

START-OF-SELECTION.

*----------------------------------------------------------------------*
* 1. Yêu cầu Khóa dữ liệu (Lock) trước khi làm bất cứ việc gì
*----------------------------------------------------------------------*
  CALL FUNCTION 'ENQUEUE_EZ_EMPLOYEE'
    EXPORTING
      mode_zemployee = 'E'            " Exclusive lock (Khóa độc quyền)
      emp_id         = p_emp_id       " Chỉ khóa đúng nhân viên này, không khóa cả bảng
    EXCEPTIONS
      foreign_lock   = 1              " Đã bị người khác khóa
      system_failure = 2
      OTHERS         = 3.

  IF sy-subrc = 1.
    " Lỗi Foreign Lock: Có người khác đang mở mã nhân viên này (Ví dụ họ đang mở SE16N)
    WRITE: / 'Nhân viên', p_emp_id, 'đang bị người khác chỉnh sửa. Vui lòng thử lại sau!'.
    RETURN.
  ELSEIF sy-subrc <> 0.
    WRITE: / 'Lỗi hệ thống khi khóa dữ liệu!'.
    RETURN.
  ENDIF.

*----------------------------------------------------------------------*
* 2. Thực hiện cập nhật Database (Update)
*----------------------------------------------------------------------*
  " Sau khi đã nắm giữ Lock, ta có thể yên tâm ghi dữ liệu
  UPDATE zemployee 
     SET phone = p_phone 
   WHERE emp_id = p_emp_id.

  IF sy-subrc = 0.
    COMMIT WORK AND WAIT. " Xác nhận giao dịch ghi vào DB
    WRITE: / 'Cập nhật số điện thoại thành công!'.
  ELSE.
    ROLLBACK WORK. " Hủy giao dịch nếu có lỗi
    WRITE: / 'Không tìm thấy mã nhân viên trong hệ thống!'.
  ENDIF.

*----------------------------------------------------------------------*
* 3. Giải phóng Khóa (Unlock)
*----------------------------------------------------------------------*
  " Bước này là BẮT BUỘC để người khác có thể vào sửa tiếp
  CALL FUNCTION 'DEQUEUE_EZ_EMPLOYEE'
    EXPORTING
      mode_zemployee = 'E'
      emp_id         = p_emp_id.

```

### 🔍 Giải thích Code Chuyên Sâu:
1. `ENQUEUE_...`: Hàm này xin hệ thống một "Ổ khóa". Nếu biến `sy-subrc = 1`, nghĩa là ổ khóa đang nằm trong tay người dùng khác (Foreign Lock). Bạn không được phép chạy lệnh UPDATE.
2. `mode = 'E' (Exclusive)`: Chế độ khóa độc quyền. Người khác không thể đọc hay ghi vào dòng dữ liệu này. (Còn có chế độ `S` - Shared, cho phép nhiều người đọc nhưng không ai được ghi).
3. `DEQUEUE_...`: Giống như việc trả chìa khóa lại cho bảo vệ. Nếu bạn quên viết hàm này, dòng dữ liệu đó sẽ bị khóa vĩnh viễn cho đến khi hệ thống bị khởi động lại hoặc có người vào T-Code `SM12` xóa khóa bằng tay. Rất nguy hiểm!
