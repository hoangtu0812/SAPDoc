# 👥 Bài 1: Tổng Quan Phân Hệ HCM (Human Capital Management)

**Mục tiêu:** Hiểu cách SAP quản lý nguồn lực quý giá nhất của doanh nghiệp - Con người.

---

## 📚 1. Phân hệ HCM là gì?
Phân hệ **SAP HCM (Human Capital Management - Quản trị Nguồn nhân lực)**, đôi khi còn gọi là SAP HR, là giải pháp toàn diện để doanh nghiệp quản lý mọi vấn đề liên quan đến nhân viên.

Từ lúc một ứng viên ứng tuyển vào công ty, được nhận vào làm, đào tạo, trả lương hàng tháng, thăng tiến, cho đến khi họ nghỉ hưu hoặc rời công ty, tất cả đều được theo dõi sát sao trong HCM.

## 🏢 2. Các thành phần chính (Sub-modules)
SAP HCM rất rộng lớn, bao gồm các phân hệ con nổi bật sau:
* **OM (Organizational Management - Quản lý Cơ cấu tổ chức):** Vẽ ra sơ đồ tổ chức của công ty (Phòng ban, Bộ phận, Vị trí chức danh). 
* **PA (Personnel Administration - Quản trị Nhân sự):** Quản lý hồ sơ nhân viên (Master Data). Dữ liệu nhân sự trong SAP được lưu theo dạng các Infotypes (Loại thông tin) như Thông tin cá nhân (IT0002), Địa chỉ (IT0006), Số tài khoản ngân hàng (IT0009).
* **PT (Personnel Time Management - Quản lý Thời gian):** Quản lý lịch làm việc, ca kíp, dữ liệu chấm công, ngày phép, đi trễ về sớm.
* **PY (Payroll - Quản lý Tiền lương):** Hệ thống tính lương cực kỳ phức tạp và mạnh mẽ, dựa trên lương cơ bản (PA), dữ liệu chấm công (PT) và các khoản phụ cấp/khấu trừ để tính ra lương thực nhận. Thuế TNCN và Bảo hiểm cũng được tính tại đây.
* **Recruitment (Tuyển dụng) / Talent Management (Quản lý tài năng):** Đăng tuyển, sàng lọc hồ sơ, đánh giá hiệu suất (KPI) và quy hoạch cán bộ.

## ⌨️ 3. Các Transaction Code (T-Code) Phổ Biến
* `PPOME` / `PPOSE`: Xem và chỉnh sửa Sơ đồ Cơ cấu tổ chức.
* `PA30`: Cập nhật/Bảo trì dữ liệu hồ sơ nhân viên (Maintain HR Master Data).
* `PA20`: Xem hồ sơ nhân viên.
* `PA40`: Chạy các Hành động nhân sự (Personnel Actions - ví dụ: Nhận nhân viên mới, Thăng chức, Nghỉ việc).
* `PT50`: Màn hình quản lý dữ liệu thời gian của nhân viên (Time Manager's Workplace).
* `PC00_M44_CALC`: Chạy chương trình tính lương (Payroll Driver) (M44 là mã quốc gia, có thể thay đổi tùy nước).

## 🔗 4. Tích hợp (Integration)
* **HCM và FI/CO:** Sau khi phân hệ PY tính xong bảng lương, dữ liệu lương (bao gồm lương cơ bản, thuế, bảo hiểm, chi phí công đoàn...) phải được Post (đẩy) sang phân hệ FI để ghi nhận chi phí lương, đồng thời đẩy sang CO (Cost Center của phòng ban đó) để quản trị chi phí.
