# 📊 Bài 4: Quy Trình Bảo Trì Đột Xuất (Breakdown) Bằng Lưu Đồ

Sự cố máy móc là không thể tránh khỏi. SAP PM thiết kế quy trình **Breakdown Maintenance** rất mượt mà để sửa chữa nhanh nhất nhưng vẫn quản lý chặt chẽ chi phí.

```mermaid
sequenceDiagram
    participant User as Người Vận Hành Máy
    participant Maint as Đội Kỹ Thuật (PM)
    participant Whse as Kho Vật Tư (MM)
    participant CO as Kế Toán Quản Trị (CO)

    Note over User, CO: 1. Phát hiện sự cố & Báo cáo
    User->>Maint: Máy Bơm P-001 đang kêu to, xì khói!
    User->>Maint: Lập Báo hỏng (Notification - IW21)
    
    Note over User, CO: 2. Lập kế hoạch sửa chữa
    Maint->>Maint: Tổ trưởng kiểm tra hiện trường, xác định hỏng Vòng bi
    Maint->>Maint: Chuyển Notification thành Lệnh sửa chữa (Work Order - IW31)
    Maint->>Whse: Gắn vật tư "Vòng bi" vào Order. Lệnh gửi yêu cầu xuất kho sang MM
    
    Note over User, CO: 3. Thực thi & Xuất kho
    Maint->>Whse: Thợ máy xuống kho lãnh đồ
    Whse->>Whse: Thủ kho xuất kho (MIGO MvT 261) tham chiếu số Order
    Whse-->>CO: Tự động hạch toán chi phí vật tư vào Work Order
    
    Note over User, CO: 4. Nghiệm thu & Báo công
    Maint->>User: Thợ thay Vòng bi xong. Lắp máy chạy lại bình thường.
    Maint->>Maint: Thợ vào hệ thống báo cáo: Mất 4 giờ sửa (Time Confirmation - IW41)
    Maint-->>CO: Tự động hạch toán tiền công (Labor Cost) vào Work Order
    Maint->>Maint: Tổ trưởng đóng Lệnh (TECO - Technical Complete)
    
    Note over User, CO: 5. Quyết toán chi phí (Settlement)
    CO->>CO: Kế toán chạy Quyết toán cuối tháng (KO88)
    Note right of CO: Toàn bộ Chi phí Vật tư + Tiền công nằm trong Work Order<br/>Được dọn sạch và trút sang Cost Center của Xưởng xài máy Bơm đó.
```

### 🔍 Giá trị quản trị của quy trình này:
1. **Kiểm soát chi phí:** Tổng chi phí bảo dưỡng (Vật tư + Nhân công) được cộng dồn vào từng thiết bị (Equipment). Cuối năm, Giám đốc mở T-Code `MCI8` thấy Máy Bơm P-001 tốn tới 50 triệu tiền sửa chữa, ngang với tiền mua máy mới => Quyết định thanh lý máy.
2. **Quản lý lịch sử (Equipment History):** Lịch sử hỏng hóc (Hỏng ngày nào, ai sửa, sửa lỗi gì) được lưu vĩnh viễn trong Notification. Hỗ trợ Đội kỹ thuật lên phương án Bảo trì phòng ngừa (Preventive Maintenance) tốt hơn trong tương lai.
