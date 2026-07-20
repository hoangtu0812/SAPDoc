# 📦 Bài 2: Thuật Ngữ và Chi Tiết T-Code Phân Hệ MM

Dưới đây là chi tiết về quản lý Vật tư và chu trình Mua sắm (Procure-to-Pay - P2P). Đây là những kiến thức bắt buộc phải biết của bất kỳ ai làm MM.

---

## 📚 1. Các Thuật Ngữ Cốt Lõi (Terminology)

* **Plant (Nhà máy / Chi nhánh):** Đơn vị tổ chức trong SAP nơi hàng hóa được sản xuất, lưu trữ hoặc phân phối. Mọi nghiệp vụ nhập xuất kho đều gắn liền với Plant.
* **Storage Location (Kho):** Khu vực lưu trữ vật lý nằm bên trong một Plant. Ví dụ: Kho nguyên vật liệu, Kho thành phẩm.
* **Purchasing Organization (Tổ chức mua hàng):** Bộ phận pháp lý chịu trách nhiệm thương lượng giá cả và điều khoản với nhà cung cấp.
* **Material Master (Dữ liệu chủ Vật tư):** Hồ sơ toàn diện về mọi thứ công ty mua, bán, sản xuất. Tùy vào phòng ban mà họ xem các "Views" (Góc nhìn) khác nhau (View Mua hàng, View Kế toán...).
* **Purchase Requisition - PR (Yêu cầu mua hàng):** Phiếu yêu cầu nội bộ từ một phòng ban (VD: Phòng IT cần mua 5 cái máy tính) gửi lên phòng Mua hàng.
* **Purchase Order - PO (Đơn đặt hàng):** Chứng từ chính thức có giá trị pháp lý gửi cho Nhà cung cấp để chốt mua số lượng và giá cả.
* **Goods Receipt - GR (Nhập kho):** Thao tác nhận hàng vật lý vào kho và ghi nhận tăng tồn kho trên hệ thống.
* **Invoice Verification - IR (Xác minh hóa đơn):** Đối chiếu Hóa đơn tài chính mà Vendor gửi tới so với Đơn đặt hàng (PO) và lượng hàng thực nhận (GR). Quy trình này gọi là 3-way matching.

---

## ⌨️ 2. Chi Tiết Chức Năng Các T-Code Quan Trọng

### A. Quản lý Dữ liệu chủ (Master Data)
* **`MM01` / `MM02` / `MM03` (Material Master):** Tạo, sửa, xem mã vật tư. Nơi khai báo đơn vị tính (Cái, Kg), nhóm vật tư, tài khoản kế toán, giá tiêu chuẩn...
* **`ME11` / `ME12` / `ME13` (Purchasing Info Record):** Tạo "bảng giá" và điều kiện thương mại lưu sẵn giữa một Vật tư và một Nhà cung cấp cụ thể. Lần sau mua sẽ tự động lấy giá này.

### B. Chuỗi Mua sắm & Kho (Purchasing & Inventory)
* **`ME51N` (Create PR):** Nhân viên kho hoặc hệ thống tự động sinh Yêu cầu mua hàng khi hàng trong kho sắp hết.
* **`ME21N` (Create PO):** Nhân viên mua hàng chuyển PR thành Đơn đặt hàng chính thức (PO) gửi cho Vendor.
* **`MIGO` (Goods Movement):** T-code quyền năng nhất của thủ kho. Dùng để Nhập kho (Goods Receipt), Xuất kho (Goods Issue), Điều chuyển kho (Transfer).
* **`MIRO` (Enter Invoice):** Kế toán công nợ dùng T-code này để gõ số tiền trên tờ hóa đơn đỏ vào SAP và đối chiếu với PO.

### C. Báo cáo (Reporting)
* **`MB52` (List of Warehouse Stocks):** Báo cáo kinh điển xem số lượng tồn kho hiện tại của vật tư, chia theo Plant và Storage Location.
* **`MB51` (Material Document List):** Xem lại toàn bộ "lịch sử" giao dịch (thẻ kho) của một vật tư (Ngày nào nhập, ngày nào xuất, ai làm).
* **`ME2M` / `ME2L`:** Danh sách các Đơn đặt hàng (PO) theo Vật tư / theo Nhà cung cấp.

---

## 🔗 3. Sự Liên Quan Giữa Các T-Code (Chu Trình P2P - Procure to Pay)

Chu trình mua hàng tiêu chuẩn (P2P) diễn ra theo đúng thứ tự T-Code sau:

1. **Nhu cầu (`ME51N`):** Phân xưởng sản xuất hết nguyên liệu, tổ trưởng dùng `ME51N` lập PR xin mua 100 kg Thép. Trạng thái: Chỉ lưu nội bộ, không có hạch toán kế toán.
2. **Đặt hàng (`ME21N`):** Phòng thu mua nhận PR, liên hệ nhà cung cấp chốt giá, sau đó tạo PO bằng `ME21N` (kế thừa dữ liệu từ PR). Gửi PO cho Vendor. Vẫn chưa có bút toán kế toán.
3. **Nhận hàng (`MIGO`):** Vendor chở thép tới cổng nhà máy. Thủ kho đếm đúng 100 kg, dùng `MIGO` (chọn MvT 101 - Nhập theo PO). 
   - *Hệ thống ngầm định:* Tăng số lượng trong báo cáo kho (`MB52`). Sinh bút toán kế toán FI (Nợ Hàng tồn kho / Có TK Trung gian GR/IR).
4. **Hóa đơn (`MIRO`):** Kế toán nhận hóa đơn GTGT 10 triệu đồng từ Vendor. Mở `MIRO`, điền mã PO vào. Hệ thống tự so sánh: PO ghi 10 triệu, kho MIGO nhập đủ 100kg, Hóa đơn 10 triệu -> Khớp hoàn toàn!
   - *Hệ thống ngầm định:* Sinh bút toán FI (Nợ TK Trung gian GR/IR / Có Công nợ Nhà cung cấp). (Khoản trung gian GR/IR được triệt tiêu).
5. **Thanh toán (`F-53` - của FI):** Cuối cùng, Kế toán FI trả tiền cho Vendor để khép lại chu trình.
