# 💻 Bài 1.2: Thực Hành Code Cơ Bản (Cú Pháp, Biến, Vòng Lặp)

Dưới đây là một chương trình ABAP hoàn chỉnh minh họa cách nhận tham số đầu vào, kiểm tra điều kiện, xử lý chuỗi và tính toán.

### 📝 Đề bài: Viết chương trình Máy tính bỏ túi

```abap
REPORT z_basic_calculator.

*----------------------------------------------------------------------*
* 1. Khai báo tham số đầu vào (Selection Screen)
*----------------------------------------------------------------------*
" Lệnh PARAMETERS tạo ra các ô nhập liệu trên màn hình
PARAMETERS: p_num1 TYPE i,          " Ô nhập số thứ 1 (Kiểu số nguyên - Integer)
            p_num2 TYPE i,          " Ô nhập số thứ 2
            p_op   TYPE c LENGTH 1. " Ô nhập phép tính (+, -, *, /) - Dài 1 ký tự

*----------------------------------------------------------------------*
* 2. Khai báo biến toàn cục
*----------------------------------------------------------------------*
DATA: gv_result  TYPE p DECIMALS 2, " Biến lưu kết quả (Kiểu Packed, 2 số thập phân)
      gv_message TYPE string.       " Biến lưu câu thông báo

*----------------------------------------------------------------------*
* 3. Xử lý Logic chính (Event START-OF-SELECTION)
*----------------------------------------------------------------------*
START-OF-SELECTION.

  " Sử dụng cấu trúc CASE...WHEN để kiểm tra phép tính
  CASE p_op.
    WHEN '+'.
      gv_result = p_num1 + p_num2.
      
    WHEN '-'.
      gv_result = p_num1 - p_num2.
      
    WHEN '*'.
      gv_result = p_num1 * p_num2.
      
    WHEN '/'.
      " Sử dụng IF để kiểm tra lỗi chia cho 0
      IF p_num2 = 0.
        WRITE: / 'Lỗi: Không thể chia cho 0!'.
        RETURN. " Thoát khỏi chương trình ngay lập tức
      ELSE.
        gv_result = p_num1 / p_num2.
      ENDIF.
      
    WHEN OTHERS.
      WRITE: / 'Lỗi: Phép tính không hợp lệ. Chỉ nhập +, -, *, /'.
      RETURN.
  ENDCASE.

  " Xử lý chuỗi (Nối chuỗi) bằng toán tử |...| (ABAP String Template)
  " String Template tự động chuyển đổi kiểu số sang chữ mà không cần lệnh chuyển đổi
  gv_message = |Kết quả phép tính { p_num1 } { p_op } { p_num2 } = { gv_result }|.

  " In kết quả ra màn hình
  WRITE: / '----- MÁY TÍNH BỎ TÚI ABAP -----'.
  WRITE: / gv_message COLOR COL_POSITIVE. " Đổi màu chữ thành màu xanh lục (Positive)
```

### 🔍 Giải thích Code:
1. `PARAMETERS`: Sinh ra giao diện người dùng. Khi chạy (F8), màn hình sẽ có 3 ô để bạn gõ số vào.
2. `TYPE p DECIMALS 2`: Nếu kết quả phép chia bị lẻ (Ví dụ: 10 / 3), hệ thống sẽ làm tròn thành 3.33 (2 số thập phân).
3. `IF p_num2 = 0`: Cực kỳ quan trọng để chống Dump (Sập chương trình) khi chia cho 0.
4. `|...|` (String Template): Kỹ thuật của ABAP 7.4+ giúp nối chuỗi rất tiện lợi bằng cách nhúng trực tiếp biến vào trong cặp dấu ngoặc nhọn `{ }`. Khuyên dùng thay thế cho lệnh `CONCATENATE` kiểu cũ.
