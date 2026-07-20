# 📊 Bài 4: Quy Trình Phân Bổ Chi Phí CO Bằng Lưu Đồ

Bài toán hóc búa nhất của Kế toán quản trị (CO) là **"Chia tiền điện chung của cả tòa nhà cho từng phòng ban như thế nào cho công bằng?"**.
Dưới đây là luồng quy trình Phân bổ (Assessment) để giải bài toán đó.

```mermaid
sequenceDiagram
    participant FI as Kế Toán FI
    participant CO_Admin as Cost Center: Tòa Nhà (Admin)
    participant CO_HR as Cost Center: Nhân Sự (HR)
    participant CO_IT as Cost Center: IT
    participant Boss as Báo Cáo Quản Trị

    Note over FI, Boss: 1. Hạch toán chi phí thực tế (Actual Posting)
    FI->>FI: Nhận hóa đơn Tiền điện 100 Triệu (FB60)
    FI->>CO_Admin: Gán toàn bộ 100 Tr vào Cost Center Tòa Nhà
    Note right of CO_Admin: Dư nợ: 100 Tr (Tiền điện)
    
    Note over FI, Boss: 2. Thiết lập quy tắc phân bổ (Allocation Rule)
    CO_Admin->>CO_Admin: Kế toán CO cài đặt SKF (Tỷ lệ phân bổ theo Diện tích m2)
    Note right of CO_Admin: Phòng HR chiếm 30% diện tích<br/>Phòng IT chiếm 70% diện tích
    
    Note over FI, Boss: 3. Chạy chu trình Phân bổ cuối tháng (Assessment - KSU5)
    CO_Admin->>CO_HR: Phân bổ 30 Tr (Bằng Secondary Cost Element)
    CO_Admin->>CO_IT: Phân bổ 70 Tr (Bằng Secondary Cost Element)
    
    Note over FI, Boss: 4. Kết quả báo cáo (KSB1)
    CO_Admin->>Boss: Báo cáo CC Tòa Nhà: Nợ 100Tr, Có 100Tr => Số dư 0
    CO_HR->>Boss: Báo cáo CC Nhân sự: Nợ 30 Tr (Phí quản lý phân bổ)
    CO_IT->>Boss: Báo cáo CC IT: Nợ 70 Tr (Phí quản lý phân bổ)
```

### 🔍 Giải thích khái niệm chuyên sâu:
1. **SKF (Statistical Key Figures):** Là các "Chỉ số thống kê" dùng làm gốc phân bổ. Ví dụ: Số lượng nhân viên, Diện tích mét vuông, Số máy lạnh... Bạn cài đặt SKF này cho từng Cost Center, khi chạy `KSU5`, SAP tự lấy tổng số tiền chia theo tỷ lệ SKF.
2. **Primary vs Secondary Cost Element:** 
   - Lúc FI nhập hóa đơn tiền điện vào Cost Center Tòa Nhà, SAP dùng **Primary Cost Element** (TK Chi phí 642).
   - Lúc CO bốc tiền từ Tòa nhà ném sang HR và IT, SAP dùng **Secondary Cost Element** (Ví dụ mã 900001 - Phí phân bổ nội bộ). Việc này giúp Giám đốc nhìn vào báo cáo HR biết ngay 30 triệu này là "Tiền phân bổ" chứ không phải "Tiền điện trực tiếp phòng HR tự xài". Nó cũng giữ cho bút toán FI không bị thay đổi.
