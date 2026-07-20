# 🏢 Bài 6: Thực Hành End-to-End: Quy Trình Accounts Payable (FI-AP)

Để hiểu rõ nghiệp vụ Kế toán phải trả (AP) trong SAP FI, bạn không thể chỉ đọc lý thuyết. Dưới đây là bài thực hành "Cầm tay chỉ việc" Từng-bước-một từ lúc tạo Vendor đến lúc tiền rời khỏi tài khoản ngân hàng.

---

## 👨‍💼 Bước 1: Tạo Hồ Sơ Nhà Cung Cấp (Vendor Master Data)
Trước khi mua hàng, bạn phải khai báo Vendor vào hệ thống.
1. Vào T-Code **`BP`** (Business Partner - Cách duy nhất trên S/4HANA, thay cho `FK01`).
2. Chọn loại BP là **Organization** (Tổ chức). Nhập tên: "Công ty Cổ Phần ABC".
3. Lưu ở Role gốc (000000). Sau đó chọn **Role: FLVN00** (FI Vendor).
4. Nhấn nút **Company Code**, nhập mã công ty `VN01`. 
5. Tại tab *Vendor: Account Management*, bắt buộc nhập **Reconciliation Account** (TK Công nợ - VD: `331000`). Bấm Save.
6. Hệ thống báo: "Business Partner 1000001 created". Bạn đã có mã Vendor.

---

## 🧾 Bước 2: Ghi Nhận Hóa Đơn Dịch Vụ Đầu Vào (T-Code: FB60)
Giả sử công ty ABC gửi cho bạn hóa đơn tiền dọn dẹp văn phòng tháng này là 10 Triệu VND, chưa có Thuế VAT 10%. (Do đây là dịch vụ, không nhập kho MM nên dùng thẳng `FB60`).
1. Vào T-Code **`FB60`**.
2. **Vendor:** Nhập mã `1000001`.
3. **Invoice Date:** Ngày xuất hóa đơn (VD: Hôm nay).
4. **Amount:** Nhập tổng tiền phải trả gồm thuế: `11,000,000`.
5. Check vào ô **Calculate Tax** (Tính thuế tự động), chọn Mã thuế `V1` (VAT 10%).
6. Ở bảng danh sách bên dưới (G/L Account lines):
   * **G/L Account:** Nhập mã TK Chi phí dịch vụ (VD: `642200`).
   * **Amount in doc.curr:** Nhập `10,000,000` (Tiền gốc trước thuế).
   * **Cost Center:** Nhập mã phòng ban xài dịch vụ (VD: `CC_ADMIN`).
7. Nhấn nút **Simulate** (Mô phỏng). Hệ thống sẽ hiện ra bút toán:
   * Nợ 642200 (Chi phí): 10,000,000
   * Nợ 133100 (Thuế GTGT): 1,000,000
   * Có 331000 (Công nợ Vendor 1000001): 11,000,000
8. Bấm **Post** (Lưu). Sinh ra chứng từ kế toán số `1900000001`.

---

## 🔍 Bước 3: Xem Báo Cáo Công Nợ (T-Code: FBL1N)
Kế toán trưởng muốn xem công ty đang nợ ai bao nhiêu tiền.
1. Vào T-Code **`FBL1N`**.
2. Nhập Vendor `1000001`, Company Code `VN01`.
3. Tick chọn mục **Open Items** (Các khoản nợ chưa trả).
4. Bấm **Execute (F8)**.
5. Màn hình hiện ra một dòng có biểu tượng **Màu Đỏ (🔴)**, số tiền `-11,000,000`. Điều này báo hiệu bạn đang nợ 11 triệu chưa thanh toán.

---

## 💸 Bước 4: Thanh Toán Cho Nhà Cung Cấp (T-Code: F-53)
Đến hạn chót, bạn dùng tiền trong tài khoản Vietcombank để trả nợ.
1. Vào T-Code **`F-53`** (Post Outgoing Payments).
2. **Document Date:** Ngày chuyển tiền.
3. Khu vực **Bank Data**:
   * **Account:** Nhập mã G/L của tài khoản Vietcombank (VD: `112101`).
   * **Amount:** `11,000,000`.
4. Khu vực **Open Item Selection**:
   * **Account:** Nhập mã Vendor `1000001`.
5. Bấm nút **Process Open Items** (Nằm phía trên cùng).
6. Hệ thống hiển thị khoản nợ 11 triệu (Màu đen/đỏ). Ở góc dưới cùng, ô **Not Assigned (Chưa phân bổ)** phải hiện bằng `0.00` (Nghĩa là Tiền trả = Tiền nợ, vừa khớp).
7. Bấm **Post** (Lưu). Sinh ra chứng từ thanh toán số `1500000001`.
*(Lúc này bút toán sinh ra: Nợ 331000 / Có 112101: 11 Triệu)*.

---

## ✅ Bước 5: Kiểm Tra Lại Trạng Thái Cấn Trừ (Clearing)
1. Quay lại T-Code **`FBL1N`**.
2. Tick chọn **Cleared Items** (Các khoản đã thanh toán), chọn ngày hôm nay.
3. Bấm **Execute**.
4. Màn hình hiện ra 2 dòng. Một dòng Hóa đơn (19...) và một dòng Thanh toán (15...), cả 2 đều có biểu tượng **Màu Xanh Lá Cây (🟢)**. Trạng thái công nợ của hóa đơn này đã chính thức đóng lại!
