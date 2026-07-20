# 📊 Bài 5: Quy Trình Mua Sắm P2P (Procure To Pay) Bằng Lưu Đồ

Để hiểu rõ sự liên kết khổng lồ giữa MM và FI, hãy xem lưu đồ hoạt động bằng **Mermaid** dưới đây. Nó mô tả chính xác sự chuyển dịch dữ liệu qua từng bước.

```mermaid
sequenceDiagram
    participant User as User/Kho (MM)
    participant Pur as Thu Mua (MM)
    participant SAP_MM as Kho SAP (Inventory)
    participant Vendor as Nhà Cung Cấp
    participant SAP_FI as Kế Toán (FI)

    Note over User, SAP_FI: 1. Nhu cầu & Đặt hàng (Không hạch toán FI)
    User->>Pur: Tạo PR (ME51N) xin mua 100kg Sắt
    Pur->>Vendor: Chốt giá, gửi PO (ME21N) 100kg x 10$
    
    Note over User, SAP_FI: 2. Nhận hàng (Tích hợp MM-FI qua OBYC)
    Vendor-->>User: Xe tải chở 100kg Sắt đến
    User->>SAP_MM: Nhập kho (MIGO MvT 101)
    SAP_MM-->>SAP_FI: Tự động hạch toán
    Note right of SAP_FI: Nợ: TK Hàng tồn kho (1000$)<br/>Có: TK Trung gian GR/IR (1000$)
    
    Note over User, SAP_FI: 3. Nhận hóa đơn (Invoice Verification)
    Vendor-->>SAP_FI: Gửi hóa đơn đỏ (1000$ + 100$ Thuế VAT)
    SAP_FI->>SAP_MM: Đối chiếu hóa đơn MIRO với mã PO
    SAP_MM-->>SAP_FI: Xác nhận khớp 100%
    Note right of SAP_FI: Nợ: TK Trung gian GR/IR (1000$)<br/>Nợ: TK Thuế GTGT (100$)<br/>Có: TK Công nợ Vendor (1100$)
    
    Note over User, SAP_FI: 4. Thanh toán (Thuần FI)
    SAP_FI->>Vendor: Lập lệnh chi tiền F-53
    Note right of SAP_FI: Nợ: TK Công nợ Vendor (1100$)<br/>Có: TK Tiền gửi Ngân hàng (1100$)
```

### 🔍 Phân tích các điểm nghẽn (Choke points) thường gặp:
1. **Lỗi ở bước MIGO:** Nếu tài khoản Hàng tồn kho trong cấu hình OBYC (Mã BSX) chưa được thiết lập cho vật tư "Sắt", lúc bấm nút Lưu trong MIGO, hệ thống sẽ báo lỗi *"Account Determination error"* và không cho nhập hàng vật lý vào.
2. **TK Trung gian GR/IR:** Chức năng của tài khoản này là để "treo" số tiền khi Hàng đã về kho nhưng Hóa đơn đỏ chưa tới (Hoặc ngược lại: Hóa đơn tới nhưng Hàng chưa về). Sau bước MIRO, tài khoản GR/IR sẽ được tất toán về 0. Nếu cuối tháng tài khoản này khác 0, kế toán phải đi tìm hiểu nguyên nhân (Hàng thất lạc hay Vendor quên xuất hóa đơn).
