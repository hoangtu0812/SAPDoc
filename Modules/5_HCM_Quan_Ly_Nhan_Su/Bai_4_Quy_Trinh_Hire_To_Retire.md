# 📊 Bài 4: Quy Trình Vòng Đời Nhân Viên (Hire-to-Retire) Bằng Lưu Đồ

Phân hệ HCM xử lý dữ liệu con người từ lúc bước chân vào công ty cho đến lúc nghỉ việc, trong đó cốt lõi nhất là luồng **Time & Payroll** (Chấm công & Tính lương).

```mermaid
sequenceDiagram
    participant OM as Quản lý Cơ cấu (OM)
    participant PA as Quản lý Hồ sơ (PA)
    participant Time as Quản lý Thời gian (PT)
    participant Payroll as Tính Lương (PY)
    participant FI as Kế Toán FI/CO

    Note over OM, FI: 1. Khởi tạo chức danh (Org Management)
    OM->>OM: PPOME: Tạo Position "Nhân viên IT" thuộc Phòng IT
    
    Note over OM, FI: 2. Nhận việc (Personnel Administration)
    PA->>OM: PA40 (Hire Action): Tạo Mã nhân viên (PERNR: 1000)
    PA->>OM: Gắn PERNR 1000 vào Position "Nhân viên IT"
    PA->>PA: Nhập Infotype 0008 (Lương Cơ Bản: 20 Triệu)
    PA->>PA: Nhập Infotype 0009 (Tài khoản Ngân hàng để trả lương)
    
    Note over OM, FI: 3. Chấm công (Time Management)
    Time->>Time: Nhân viên quẹt thẻ hàng ngày ở cửa ra vào
    Time->>Time: Trưởng phòng duyệt Phép năm qua cổng Fiori (ESS/MSS)
    Time->>Payroll: Cuối tháng chạy Time Evaluation (PT60) chốt công: Làm 20 ngày, nghỉ 2 ngày, OT 10 tiếng
    
    Note over OM, FI: 4. Tính Lương (Payroll Engine)
    Payroll->>Payroll: Chạy PC00_M99_CALC
    Payroll->>Payroll: Schema đọc Lương CB (PA) + Ngày công (Time) => Gross Pay (Lương Gộp)
    Payroll->>Payroll: Tính Thuế TNCN (PIT), Trừ BHXH => Net Pay (Lương Thực Nhận)
    
    Note over OM, FI: 5. Hạch toán (Posting to Accounting)
    Payroll->>FI: Chạy đẩy lương sang FI
    Note right of FI: Nợ: Chi phí lương (Cost Center Phòng IT)<br/>Có: Phải trả người lao động (Net Pay)<br/>Có: Bảo hiểm xã hội phải nộp
    FI->>PA: Ngân hàng chi tiền xong, File Payslip PDF gửi vào email nhân viên
```

### 🔍 Giải thích khái niệm chuyên sâu:
1. **ESS / MSS (Employee / Manager Self-Service):** Ứng dụng cổng thông tin (Fiori) cho phép nhân viên tự xin nghỉ phép, tự xem phiếu lương (ESS). Trưởng phòng tự vào duyệt phép, đánh giá KPI cho lính (MSS) mà không cần bộ phận HR nhập liệu hộ (PA30).
2. **Gross-to-Net (Từ Lương gộp xuống Lương thực nhận):** Trái tim của Payroll Engine. Nó xử lý hàng trăm quy tắc luật pháp phức tạp (Như tính thuế lũy tiến từng phần, giới hạn trần đóng BHXH) tự động bằng Schema và Rules.
