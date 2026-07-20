# 💰 Bài 5: Thực Hành End-to-End: Nhận Việc & Tính Lương (HCM Payroll)

Bài thực hành này mô phỏng công việc của một chuyên viên Nhân sự (HR) và C&B. Từ việc nhập thông tin một nhân sự mới vào làm, ghi nhận giờ công và chạy ra phiếu lương.

---

## 🙋‍♂️ Bước 1: Tiếp Nhận Nhân Viên Mới (T-Code: PA40)
Ứng viên vừa đậu phỏng vấn, HR cần tạo hồ sơ trên hệ thống.
1. Vào T-Code **`PA40`** (Personnel Actions).
2. Ở ô **Personnel No.** để trống (Hệ thống sẽ tự cấp mã).
3. **Start Date:** Nhập ngày đi làm đầu tiên (VD: 01/10/2023).
4. Ở danh sách Action Type bên dưới, chọn **Hire** (Nhận việc). Bấm Execute (F8).
5. **Điều kỳ diệu của PA40:** Nó không mở 1 màn hình, mà mở liên tiếp chuỗi các màn hình Infotype (IT) để bạn không bị quên.
   * **Màn hình 1 (IT0000 - Actions):** Bấm Save.
   * **Màn hình 2 (IT0002 - Personal Data):** Nhập Tên, Họ, Ngày sinh, Tình trạng hôn nhân. Bấm Save.
   * **Màn hình 3 (IT0001 - Org. Assignment):** Chọn Personnel Area (Hà Nội), Employee Group (Full-time). Hệ thống sẽ sinh ra **Mã nhân viên (VD: 10005)**. Bấm Save.
   * **Màn hình 4 (IT0008 - Basic Pay):** Cực kỳ quan trọng. Chọn Bậc lương (Pay Scale). Ở phần Wage Type bên dưới, gõ mã `1000` (Lương cơ bản), nhập số tiền: `20,000,000` VND. Bấm Save.
   * **Màn hình 5 (IT0009 - Bank Details):** Nhập số tài khoản ngân hàng của nhân viên để cuối tháng chuyển khoản. Bấm Save.
6. Xong! Bạn đã hoàn thành quy trình Onboarding trên hệ thống.

---

## ⏱️ Bước 2: Chốt Công Cuối Tháng (T-Code: PT60)
Ngày 30 hàng tháng, bộ phận C&B phải chốt dữ liệu quẹt thẻ.
1. Khởi chạy T-Code **`PT60`** (Time Evaluation).
2. Nhập Mã nhân viên `10005`.
3. **Evaluation Schema:** Chọn Schema chấm công chuẩn của công ty (VD: `TM00` - Quản lý thời gian có máy quẹt thẻ).
4. **Evaluation up to:** `30.10.2023`.
5. Bấm **Execute**. Hệ thống sẽ đọc dữ liệu máy chấm công (Lưu ở IT2011), đối chiếu với ca làm việc chuẩn (IT0007). Nó tự trừ ra số lần đi trễ, tự cộng số giờ OT. Dữ liệu này được đẩy vào Cụm dữ liệu thời gian (Time Cluster) để chờ tính lương.

---

## 💵 Bước 3: Chạy Tính Lương (T-Code: PC00_M99_CALC)
Đây là "quái vật" mạnh nhất của SAP HCM.
1. Khởi chạy T-Code **`PC00_M99_CALC`** (Mã 99 là quốc tế, nếu xài phiên bản VN chuẩn của SAP thì là PC00_M40_CALC).
2. **Payroll Area:** Nhập khu vực tính lương (VD: Nhóm lãnh lương ngày 5 hàng tháng).
3. **Payroll Period:** Nhập tháng cần tính (VD: Tháng 10/2023).
4. **Personnel Number:** `10005`.
5. Check vào ô **Test Run** (Chạy thử trước để xem có lỗi không, không lưu vào Database).
6. Bấm **Execute**.
7. Một bảng kết quả hình cây hiện ra. Bạn click đúp vào chữ **"RT" (Result Table)**. Ở đây bạn sẽ thấy hệ thống tự động bóc tách:
   * Wage Type 1000 (Lương cứng): 20,000,000
   * Wage Type 2000 (Lương OT): 1,500,000 (Do lấy công thức Thời gian từ PT60 nhân với Lương CB).
   * Wage Type 3000 (Thuế TNCN): -1,200,000 (Bị trừ đi).
   * **Wage Type /559 (Bank Transfer): 20,300,000 (Lương thực nhận chuyển khoản)**.
8. Bỏ Check "Test Run", chạy lại lần nữa để chốt sổ (Lưu thật).

---

## 🏦 Bước 4: Chuyển Lương Sang Tài Chính (T-Code: PC00_M99_CIPE)
Lương đã tính xong bên HR, nhưng sếp tài chính (FI) cần ghi nhận chi phí vào báo cáo công ty.
1. Chạy T-Code **`PC00_M99_CIPE`** (Posting to Accounting).
2. Nhập các tham số Tháng, Mã nhân viên tương tự.
3. Bấm **Execute**.
4. SAP sẽ dò bảng Mapping (Cấu hình ở SPRO Bài 3), tự động nhóm tất cả lương của phòng IT lại và sinh ra 1 bút toán FI duy nhất chuyển sang hệ thống Kế toán:
   * Nợ 6422 (Chi phí lương nhân viên IT): 21,500,000 (Gắn với Cost Center Phòng IT)
   * Có 3341 (Phải trả người lao động): 20,300,000
   * Có 3335 (Thuế TNCN phải nộp): 1,200,000
5. Cuối cùng, kế toán dùng T-Code `F-53` làm lệnh giải ngân 20,300,000 từ tài khoản Vietcombank để trả cho nhân viên. Xong!
