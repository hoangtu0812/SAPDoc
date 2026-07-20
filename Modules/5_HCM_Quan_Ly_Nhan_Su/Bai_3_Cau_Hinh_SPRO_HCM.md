# ⚙️ Bài 3: Hướng Dẫn Cấu Hình Hệ Thống (SPRO) Phân Hệ HCM

**Mục tiêu:** Nắm được cách thiết lập khung xương cho tổ chức nhân sự và cấu hình bảng tính lương (Payroll) trong SAP HCM.

Sử dụng T-Code **`SPRO`** -> SAP Reference IMG.

---

## 1. Cấu trúc Nhân sự (Enterprise & Personnel Structure)

SAP HCM chia cấu trúc thành 2 loại: Cấu trúc Doanh nghiệp (Về mặt vật lý) và Cấu trúc Nhân sự (Về mặt hợp đồng).

**Đường dẫn:** `Enterprise Structure -> Definition -> Human Resources`
* **Personnel Areas (Khu vực nhân sự):** Tạo các khu vực (Ví dụ: `HN01` - Văn phòng Hà Nội, `HCM1` - Văn phòng Hồ Chí Minh).
* **Personnel Subareas:** Chia nhỏ khu vực ra (Ví dụ: Dưới HN01 có `VP` - Văn phòng, `SX` - Xưởng sản xuất).

**Đường dẫn:** `Personnel Management -> Personnel Administration -> Organizational Data -> Personnel Structure`
* **Employee Groups (Nhóm nhân viên):** Khai báo các nhóm (Ví dụ: `1` - Nhân viên toàn thời gian, `2` - Thực tập, `3` - Freelancer).
* **Employee Subgroups:** Cấp nhỏ hơn (Ví dụ: `1A` - Khối Hành chính, `1B` - Khối Kỹ thuật).
* Hệ thống sẽ dựa vào sự kết hợp giữa Subarea và Subgroup để quyết định Lịch làm việc và Bậc lương.

---

## 2. Quản lý Thời gian (Time Management)

**Đường dẫn:** `Time Management -> Work Schedules`
* **Public Holidays:** Khai báo danh sách các ngày lễ quốc gia (Tết, Giỗ tổ, 30/4) để hệ thống không trừ phép nếu nhân viên nghỉ ngày này.
* **Daily Work Schedules (Ca làm việc):** Khai báo giờ vào/ra. (Ví dụ: Ca Hành chính từ 08:00 - 17:00, nghỉ trưa 12:00 - 13:00).
* **Work Schedule Rules:** Khai báo chu kỳ xoay ca (Ví dụ: Làm 5 ngày nghỉ T7 CN, hoặc công nhân xoay ca 1-2-3).

---

## 3. Cấu hình Lương (Payroll - Vô cùng phức tạp)

Payroll của SAP HCM là một trong những engine tính toán mạnh nhất thế giới. Nó không dùng bảng biểu cấu hình thông thường mà dùng **Schema** và **Rules**.

**Giao diện cấu hình Lương (T-Code `PE01` cho Schema, `PE02` cho Rules):**
* **Schema tính lương:** Là một "đoạn code" kịch bản chạy dài dằng dặc (Ví dụ Schema chuẩn của quốc tế là `X000`).
* Nó quy định các bước:
  1. Kéo lương cơ bản từ Infotype 0008.
  2. Kéo số giờ làm việc từ Time Management.
  3. Tính toán bằng các **Wage Types** (Loại lương). (VD: WT 1000 = Lương cơ bản, WT 2000 = Phụ cấp, WT 3000 = Đóng BHXH).
* Chuyên gia HCM (Payroll Consultant) sẽ ngồi sửa Schema, viết thêm các Rule (như câu lệnh IF-ELSE) để tính toán ngầm định bên trong.

**Đường dẫn SPRO:** `Payroll -> Payroll: International -> Basic Settings`
* Cấu hình các **Wage Types** (Loại lương): Khai báo WT này là khoản Thu nhập hay Khấu trừ, có phải đóng thuế hay không.

---

## 4. Tích hợp HCM-FI (Đẩy chi phí lương)

**Đường dẫn:** `Payroll -> Payroll: International -> Reporting for Posting Payroll Results to Accounting`

* Bước này HR Consultant phải làm việc với FI Consultant.
* Khai báo sự liên kết (Mapping): Wage Type nào sẽ được định khoản vào Tài khoản sổ cái G/L nào.
* *Ví dụ:* 
  * WT 1000 (Lương cơ bản) -> Post vào FI: Nợ TK Chi phí lương (642) / Có TK Phải trả NLĐ (334).
  * WT 3000 (BHXH) -> Post vào FI: Nợ TK 334 / Có TK Bảo hiểm phải nộp (338).
