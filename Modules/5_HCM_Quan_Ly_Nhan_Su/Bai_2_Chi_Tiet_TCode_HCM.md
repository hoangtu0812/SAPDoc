# 👥 Bài 2: Thuật Ngữ và Chi Tiết T-Code Phân Hệ HCM

Phân hệ Nhân sự (HCM) của SAP nổi tiếng với lượng dữ liệu khổng lồ và độ bảo mật khắt khe. Dưới đây là cách SAP tổ chức và vận hành mảng dữ liệu đặc thù này.

---

## 📚 1. Các Thuật Ngữ Cốt Lõi (Terminology)

* **Personnel Area (Khu vực nhân sự):** Phân khu địa lý hoặc hành chính của tổ chức (Ví dụ: Chi nhánh Hà Nội, Chi nhánh HCM).
* **Employee Group / Subgroup (Nhóm nhân viên):** Phân loại nhân viên (Ví dụ: Nhân viên chính thức, Thực tập sinh, Nhân viên hợp đồng, Công nhân ca kíp). Điều này quyết định chế độ lương thưởng và ngày phép của họ.
* **Infotype (Loại thông tin):** Khái niệm mang tính biểu tượng của SAP HCM. Hồ sơ nhân viên được chia thành hàng trăm "mảnh ghép" gọi là Infotypes (IT), mỗi IT là một màn hình nhập liệu gồm 4 số.
  * `IT0000`: Actions (Các hành động như Nhận việc, Đuổi việc).
  * `IT0001`: Org. Assignment (Gán vào phòng ban nào).
  * `IT0002`: Personal Data (Tên, ngày sinh, CMND).
  * `IT0008`: Basic Pay (Mức lương cơ bản).
  * `IT0009`: Bank Details (Số tài khoản ngân hàng để trả lương).
* **PERNR (Personnel Number):** Mã nhân viên - Mã số duy nhất định danh một người xuyên suốt hệ thống.
* **Time Evaluation (Đánh giá thời gian):** Hệ thống tổng hợp dữ liệu quẹt thẻ ra/vào, so khớp với lịch làm việc (Work Schedule) để chốt số giờ làm thêm (OT), số giờ đi trễ.
* **Payroll Area (Khu vực tính lương):** Gom nhóm những người nhận lương cùng một thời điểm (Ví dụ: Công nhân nhận lương ngày 15, Khối văn phòng nhận lương ngày cuối tháng).

---

## ⌨️ 2. Chi Tiết Chức Năng Các T-Code Quan Trọng

### A. Cơ Cấu Tổ Chức (Organizational Management - OM)
* **`PPOME` / `PPOSE` (Organization and Staffing):** Màn hình trực quan để vẽ và xem sơ đồ hình cây của công ty (Phòng ban -> Nhóm -> Vị trí chức danh).
* **`PO10` (Maintain Organizational Unit):** Bảo trì thông tin chuyên sâu của một Phòng ban.
* **`PO13` (Maintain Position):** Bảo trì thông tin chuyên sâu của một Vị trí (VD: Trưởng phòng IT).

### B. Quản Trị Nhân Sự (Personnel Administration - PA)
* **`PA40` (Personnel Actions):** Khởi chạy một "Hành động". Khi chạy `PA40` Hire (Nhận việc), hệ thống sẽ tự động mở liên tiếp các màn hình Infotype (0000 -> 0001 -> 0002 -> 0008) để HR nhập liệu tuần tự mà không bị sót.
* **`PA30` (Maintain HR Master Data):** Cập nhật dữ liệu đơn lẻ. VD: Nhân viên đổi số tài khoản ngân hàng, HR vào PA30 gõ mã số `0009` (Bank Details) để sửa lại số tài khoản cho họ.
* **`PA20` (Display HR Master Data):** Chỉ cho phép xem hồ sơ (View only).

### C. Thời Gian & Lương (Time & Payroll)
* **`PT50` (Time Manager's Workplace):** Trạm làm việc của người làm công. Xem bảng chấm công, phê duyệt ngày phép, xem lỗi quẹt thẻ.
* **`PT60` (Time Evaluation):** Chạy chương trình chốt công cuối tháng.
* **`PC00_M99_CALC` (Payroll Driver):** (Số 99 là mã quốc tế, có thể thay bằng mã quốc gia khác). Đây là nút "bấm" để hệ thống xoay dữ liệu lương, tính toán các thuật toán thuế và cho ra Phiếu lương (Payslip).

---

## 🔗 3. Sự Liên Quan Giữa Các T-Code (Quy Trình Hire to Retire)

Quy trình vòng đời nhân viên (Từ lúc vào công ty đến lúc nhận lương):

1. **Thiết kế tổ chức (`PPOME`):** HR dùng `PPOME` tạo ra một Position trống "Chuyên viên Lập trình ABAP" thuộc Phòng IT.
2. **Nhận việc (`PA40`):** Có ứng viên đậu phỏng vấn. HR chạy `PA40` hành động "Hire". Hệ thống cấp mã số nhân viên (PERNR = 1005). HR gắn mã 1005 vào Position vừa tạo. Ghi nhận lương cơ bản vào `IT0008`.
3. **Cập nhật thông tin (`PA30`):** Cuối tuần, nhân viên nộp thẻ ngân hàng mới, HR dùng `PA30` mở Infotype `0009` nhập số tài khoản vào.
4. **Chấm công (`PT50` & `PT60`):** Trong tháng, nhân viên đi làm, quẹt thẻ. HR dùng `PT50` kiểm tra và chạy `PT60` chốt công ra được 20 ngày làm việc và 10 tiếng OT.
5. **Tính lương (`PC00_Mxx_CALC`):** Ngày cuối tháng, bộ phận C&B chạy chương trình lương. Hệ thống móc lương cơ bản (từ `IT0008`) + Thời gian làm việc (từ kết quả `PT60`) tính ra lương thực nhận.
6. **Hạch toán chi phí:** Cuối cùng, kết quả lương được đẩy tự động sang phân hệ FI (tạo bút toán Nợ Chi phí lương / Có Phải trả NLĐ) và CO (Ghi nợ Cost Center Phòng IT).
