# 💻 Bài 9.1: Thực Hành Code Cải Tiến Bằng BAdI (Business Add-Ins)

BAdI là công nghệ hiện đại nhất của SAP (dùng lập trình Hướng đối tượng - OOP) để can thiệp vào mã nguồn tiêu chuẩn mà không làm hỏng chúng.

### 📝 Đề bài: Công ty yêu cầu: "Khi người dùng tạo Đơn hàng bán (VA01), nếu giá trị đơn hàng vượt quá 1 tỷ VNĐ thì tự động hiện cảnh báo lên màn hình."

SAP đã cung cấp sẵn BAdI tên là `BADI_SD_SALES_BASIC` được cắm sẵn ở sự kiện "Trước khi lưu Đơn hàng". Chúng ta chỉ việc viết code "nhúng" (Implement) vào BAdI này.

Dưới đây là mã nguồn bạn sẽ viết trong Method của Class Implement BAdI.

```abap
*----------------------------------------------------------------------*
* CLASS: ZCL_IM_BADI_SD_SALES_BASIC
* METHOD: IF_EX_BADI_SD_SALES_BASIC~BEFORE_SAVE
*----------------------------------------------------------------------*
  METHOD if_ex_badi_sd_sales_basic~before_save.
  
    " Khai báo biến
    DATA: lv_total_value TYPE vbak-netwr,
          lv_msg         TYPE string.

    " 1. Lấy dữ liệu Đơn hàng từ tham số truyền vào của BAdI
    " Trong BAdI này, SAP truyền cho ta một Structure chứa thông tin Đơn hàng là IS_VBAK
    
    " Kiểm tra xem đây có phải là Đơn hàng bán nội địa (Ví dụ Document Type = 'OR') không?
    IF is_vbak-auart = 'OR'.
    
      " 2. Tính tổng giá trị đơn hàng (SAP đã tính sẵn trong field NETWR)
      lv_total_value = is_vbak-netwr.
      
      " 3. Áp dụng Logic của doanh nghiệp
      " Giả sử đồng tiền là VND, kiểm tra > 1 Tỷ (1,000,000,000)
      IF lv_total_value > 1000000000 AND is_vbak-waerk = 'VND'.
      
        " Hiện thông báo Cảnh báo (Warning) cho User
        " Lưu ý: Cảnh báo (W) cho phép User nhấn Enter để bỏ qua và đi tiếp.
        " Nếu dùng Lỗi (E), User sẽ bị chặn đứng không cho Save.
        
        MESSAGE 'Cảnh báo: Đơn hàng này có giá trị cực lớn (> 1 Tỷ VND). Vui lòng kiểm tra lại công nợ khách hàng!' 
          TYPE 'W' DISPLAY LIKE 'I'. " Hiển thị dạng Information box (I) cho đẹp
          
      ENDIF.
      
    ENDIF.

  ENDMETHOD.
```

### 🔍 Hướng Dẫn Các Bước Triển Khai Thực Tế:
Dĩ nhiên, bạn không thể tạo một chương trình `SE38` rồi ném cục code này vào là nó tự chạy. Bạn phải thao tác qua GUI:
1. Vào T-Code **`SE18`**, gõ tên BAdI Definition: `BADI_SD_SALES_BASIC`.
2. Chọn **Implementation -> Create**. Đặt tên cho Implementation của bạn (Ví dụ: `ZIM_SD_SALES_WARN`).
3. Hệ thống sẽ hỏi bạn đặt tên cho Class xử lý. Nhập `ZCL_IM_BADI_SD_SALES_BASIC`.
4. Mở Class đó ra, bạn sẽ thấy hàng loạt các Method trống (Do SAP định nghĩa sẵn trong Interface).
5. Kích đúp vào Method `BEFORE_SAVE`.
6. Copy dán đoạn code phía trên vào, Lưu (Save) và Kích hoạt (Activate).
7. Quay ra ngoài màn hình BAdI Implementation, bấm nút **Activate** toàn bộ BAdI.

Bây giờ, cứ ai vào VA01 lưu đơn > 1 Tỷ, đoạn code này sẽ tự động được hệ thống đánh thức và kích hoạt!
