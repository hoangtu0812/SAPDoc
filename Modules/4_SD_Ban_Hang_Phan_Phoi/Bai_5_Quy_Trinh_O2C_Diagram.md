# 📊 Bài 5: Quy Trình Bán Hàng O2C (Order To Cash) Bằng Lưu Đồ

Bán hàng không chỉ là gửi sản phẩm đi. Nó là quy trình kết nối chặt chẽ giữa bộ phận Bán hàng (SD), Kho (MM) và Tài chính (FI). Dưới đây là lưu đồ chi tiết minh họa toàn bộ các bút toán.

```mermaid
sequenceDiagram
    participant Cust as Khách Hàng
    participant SD as Sale Admin (SD)
    participant MM as Kho/Vận chuyển (MM)
    participant FI as Kế Toán (FI)

    Note over Cust, FI: 1. Tạo đơn hàng (Kiểm tra ATP & Pricing)
    Cust->>SD: Đặt mua 50 cái Laptop
    SD->>SD: Tạo Sales Order (VA01)
    SD->>MM: Check Tồn kho ATP (Ngầm định)
    MM-->>SD: Đủ hàng!
    SD->>SD: Check Pricing V/08: 50 x 20$ = 1000$
    
    Note over Cust, FI: 2. Giao hàng & Xuất kho (Tích hợp SD-MM-FI)
    SD->>MM: Yêu cầu giao hàng (VL01N)
    MM->>Cust: Đóng gói & Xếp hàng lên xe tải
    MM->>MM: Bấm nút PGI (Post Goods Issue) trong VL02N
    MM-->>FI: Báo cáo lượng kho giảm sút
    Note right of FI: Nợ: TK Giá vốn hàng bán (VD: 800$)<br/>Có: TK Hàng tồn kho (800$)
    
    Note over Cust, FI: 3. Xuất hóa đơn (Tích hợp SD-FI qua VKOA)
    SD->>SD: Tạo Hóa đơn Billing (VF01)
    SD-->>FI: Chuyển dữ liệu Billing sang FI
    Note right of FI: Nợ: TK Công nợ khách hàng (1100$)<br/>Có: TK Doanh thu (1000$)<br/>Có: TK Thuế GTGT (100$)
    FI->>Cust: Gửi bản PDF Hóa đơn
    
    Note over Cust, FI: 4. Khách thanh toán
    Cust-->>FI: Chuyển khoản ngân hàng 1100$
    FI->>FI: Ghi nhận thu tiền (F-28)
    Note right of FI: Nợ: TK Tiền gửi Ngân hàng (1100$)<br/>Có: TK Công nợ khách hàng (1100$)
```

### 🔍 Các điểm trọng yếu cần lưu ý (Key Takeaways):
1. **Lệnh tạo Sales Order (VA01) KHÔNG sinh ra bút toán FI.** Nó chỉ là cam kết.
2. **PGI (Post Goods Issue)** trong `VL02N` là thao tác cực kỳ quan trọng. Lúc này, quyền sở hữu hàng hóa chính thức chuyển giao sang khách hàng. Hệ thống trừ tồn kho bên MM (`MB52`), và sinh bút toán tài chính **ghi nhận Giá vốn (COGS)**. 
3. **Billing (VF01)** là bước sinh **Doanh thu**. Hệ thống tự động xác định Tài khoản doanh thu tương ứng nhờ bảng cấu hình **VKOA** (Tùy thuộc vào Nhóm vật tư và Nhóm khách hàng).
