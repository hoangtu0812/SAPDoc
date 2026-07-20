# 📈 Bài 2: Thuật Ngữ và Chi Tiết T-Code Phân Hệ CO

Khác với FI là bộ mặt tài chính đối ngoại, CO (Kế toán Quản trị) giống như một mạng lưới giám sát "nội bộ" chằng chịt và cực kỳ chi tiết. Hãy cùng tìm hiểu.

---

## 📚 1. Các Thuật Ngữ Cốt Lõi (Terminology)

* **Controlling Area (Vùng kiểm soát):** Đơn vị tổ chức cao nhất trong CO. Một Controlling Area có thể gán với nhiều Company Code (FI), cho phép báo cáo quản trị xuyên quốc gia/xuyên công ty.
* **Cost Center (Trung tâm chi phí):** Một phòng ban, bộ phận hoặc khu vực phát sinh chi phí trong công ty (Ví dụ: Phòng Marketing, Phân xưởng sản xuất A).
* **Profit Center (Trung tâm lợi nhuận):** Một khu vực quản lý chịu trách nhiệm về cả Doanh thu và Chi phí, từ đó tính ra Lợi nhuận (Ví dụ: Chi nhánh Hà Nội, Dòng sản phẩm Điện thoại).
* **Cost Element (Khoản mục chi phí):** Bản sao của Tài khoản Sổ cái bên FI, nhưng dùng cho CO. Có 2 loại:
  * **Primary (Sơ cấp):** Có kết nối trực tiếp 1-1 với tài khoản Chi phí bên FI (VD: Chi phí lương).
  * **Secondary (Thứ cấp):** Chỉ tồn tại bên CO dùng để phân bổ chi phí nội bộ giữa các phòng ban (Không ảnh hưởng FI).
* **Internal Order (Đơn hàng nội bộ):** Một đối tượng tạm thời để thu thập chi phí cho một chiến dịch hoặc công việc cụ thể (Ví dụ: Chi phí sửa chữa xe tải B, Chi phí sự kiện Year-end party).
* **Assessment / Distribution (Phân bổ chi phí):** Quy trình cuối tháng lấy chi phí gom chung ở một Cost Center (VD: Tiền điện tổng công ty) chia nhỏ và gán về cho các Cost Center sử dụng thực tế (VD: Phòng HCNS 10%, Phòng IT 30%, Phân xưởng 60%).

---

## ⌨️ 2. Chi Tiết Chức Năng Các T-Code Quan Trọng

### A. Quản lý Dữ liệu chủ (Master Data)
* **`KS01` / `KS02` / `KS03` (Cost Center):** Tạo, sửa, xem Trung tâm chi phí.
* **`KE51` / `KE52` / `KE53` (Profit Center):** Tạo, sửa, xem Trung tâm lợi nhuận.
* **`KA01` / `KA06` (Cost Element):** Tạo khoản mục chi phí Sơ cấp (`KA01`) hoặc Thứ cấp (`KA06`). *(Từ S/4HANA, T-code này đã gộp chung vào `FS00`)*.
* **`KO01` / `KO02` / `KO03` (Internal Order):** Tạo Đơn hàng nội bộ thu thập chi phí tạm thời.

### B. Hạch toán và Phân bổ (Transactions & Allocations)
* **`KB11N` (Manual Reposting of Costs):** Điều chuyển chi phí bằng tay từ Cost Center này sang Cost Center khác do hạch toán nhầm ban đầu.
* **`KSU5` (Execute Assessment):** Chạy chu trình phân bổ (Assessment) tự động cuối tháng để chia chi phí gián tiếp (như tiền điện, tiền thuê nhà) cho các phòng ban khác bằng Cost element thứ cấp.
* **`KSV5` (Execute Distribution):** Chạy chu trình phân bổ (Distribution), nhưng giữ nguyên Cost element sơ cấp gốc.

### C. Báo cáo (Reporting)
* **`KSB1` (Cost Centers: Actual Line Items):** Báo cáo cực kỳ quan trọng, hiển thị chi tiết từng dòng chi phí (bút toán) đã "rơi" vào một Cost Center cụ thể.
* **`S_ALR_87013611` (Cost Center: Actual/Plan/Variance):** Báo cáo đối chiếu giữa Kế hoạch (Ngân sách dự kiến) và Thực tế (Chi phí đã xài), từ đó tính ra độ chênh lệch (Variance) để kiểm soát.

---

## 🔗 3. Sự Liên Quan Giữa Các T-Code (Quy Trình Quản Trị)

### Luồng 1: Hạch toán và Điều chỉnh chi phí
1. Trước khi làm việc, Kế toán CO dùng **`KS01`** tạo Cost Center "Phòng Nhân Sự" (CC_HR) và "Phòng IT" (CC_IT).
2. Kế toán FI dùng `FB50` hạch toán chi phí mua văn phòng phẩm và gán nhầm vào Cost Center của "Phòng IT".
3. Kế toán quản trị phát hiện lỗi bằng cách chạy báo cáo **`KSB1`** của Phòng IT.
4. Kế toán quản trị dùng **`KB11N`** để chuyển số tiền đó từ CC_IT sang trả lại cho CC_HR.

### Luồng 2: Phân bổ tiền điện cuối tháng (Assessment)
1. Hóa đơn tiền điện toàn tòa nhà gửi về, kế toán ghi nhận vào một Cost Center chung "Quản lý Tòa nhà" (CC_ADMIN) bằng `FB60`.
2. Cuối tháng, Kế toán CO chạy **`KSU5`** theo tỷ lệ mét vuông đã cài sẵn.
3. Kết quả: Chi phí từ CC_ADMIN được rút ra (ghi Có) và đẩy xuống cho các phòng ban CC_HR, CC_IT (ghi Nợ). 
4. Chạy lại báo cáo **`S_ALR_87013611`**, Giám đốc sẽ thấy rõ chính xác mỗi phòng ban tốn bao nhiêu tiền điện trong tháng để so với ngân sách.
