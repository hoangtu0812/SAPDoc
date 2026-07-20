# 📊 Bài 5: Quy Trình Nghiệp Vụ FI Tiêu Biểu Bằng Lưu Đồ

Phân hệ FI có rất nhiều quy trình, nhưng tiêu biểu nhất là quy trình **Quản lý Khoản Phải Trả (Accounts Payable - AP)**. Đây là luồng theo dõi công nợ và chi tiền cho Nhà cung cấp.

```mermaid
sequenceDiagram
    participant MM as Phân hệ MM
    participant FI_AP as Kế Toán Công Nợ (AP)
    participant FI_GL as Kế Toán Tổng Hợp (GL)
    participant Bank as Ngân hàng House Bank
    participant Vendor as Nhà Cung Cấp

    Note over MM, Vendor: 1. Ghi nhận hóa đơn đầu vào (MIRO / FB60)
    MM->>FI_AP: Chuyển Hóa đơn mua hàng (MIRO)
    FI_AP->>FI_AP: Hoặc kế toán tự gõ hóa đơn dịch vụ (FB60)
    FI_AP-->>FI_GL: Hệ thống sinh bút toán sổ cái ngầm định
    Note right of FI_GL: Nợ: TK Chi phí / TK GR/IR<br/>Có: TK Công nợ Vendor (Màu đỏ - Open Item)
    
    Note over MM, Vendor: 2. Đề nghị thanh toán & Duyệt chi
    FI_AP->>FI_AP: Mở báo cáo FBL1N xem danh sách nợ đến hạn
    FI_AP->>FI_AP: Kế toán chạy chương trình thanh toán tự động (F110)
    FI_AP->>Bank: F110 sinh ra file thanh toán (XML) gửi tới Ngân hàng
    
    Note over MM, Vendor: 3. Hạch toán Chi tiền (Clearing)
    Bank-->>Vendor: Ngân hàng chuyển tiền cho nhà cung cấp
    FI_AP->>FI_AP: Kế toán dùng F-53 (hoặc EBS) hạch toán chi tiền
    FI_AP-->>FI_GL: Sinh bút toán
    Note right of FI_GL: Nợ: TK Công nợ Vendor<br/>Có: TK Tiền gửi ngân hàng
    FI_GL->>FI_AP: Hệ thống tự động Clear (Cấn trừ) công nợ
    Note right of FI_AP: FBL1N: Trạng thái chuyển từ Đỏ (Open) sang Xanh (Cleared)
```

### 🔍 Điểm mấu chốt:
1. **Open Item Management (Quản lý hạng mục mở):** Tài khoản Công nợ Vendor luôn được cài đặt tính năng Open Item. Khi mới phát sinh nợ, nó có màu Đỏ. Chừng nào kế toán chưa hạch toán chi tiền (Credit/Debit bằng nhau), nó sẽ mãi mãi màu đỏ. Chỉ khi đối trừ xong (Clearing), nó mới chuyển thành Xanh lá.
2. **T-Code F110 (Automatic Payment Program):** Các tập đoàn lớn không chi tiền lắt nhắt từng món (`F-53`). Họ gom hàng ngàn hóa đơn đến hạn vào chạy `F110` một lần, hệ thống tự động sinh ra một file Ủy nhiệm chi khổng lồ đẩy thẳng sang hệ thống core của Vietcombank/BIDV để giải ngân.
