# ⚙️ Bài 3: Hướng Dẫn Cấu Hình Hệ Thống (SPRO) Phân Hệ SD

**Mục tiêu:** Hiểu cách thiết lập bộ khung cấu trúc bán hàng và cấu hình Pricing Procedure (thủ tục tính giá) cực kỳ quyền năng của SD.

Sử dụng T-Code **`SPRO`** -> SAP Reference IMG.

---

## 1. Cấu trúc Bán hàng (Sales Enterprise Structure)

**Đường dẫn:** `Enterprise Structure -> Definition -> Sales and Distribution`
* **Define Sales Organization (`OVX5`):** Tạo Đơn vị bán hàng (Ví dụ: `SO01` - Công ty Bán Lẻ VN).
* **Define Distribution Channel (`OVXI`):** Tạo Kênh phân phối (Ví dụ: `10` - Bán sỉ, `20` - Bán lẻ, `30` - Online).
* **Define Division (`OVXB`):** Tạo Ngành hàng (Ví dụ: `D1` - Thực phẩm, `D2` - Hóa mỹ phẩm).

**Đường dẫn:** `Enterprise Structure -> Assignment -> Sales and Distribution`
* **Assign Sales Org to Company Code (`OVX3`):** Trỏ tổ chức bán hàng về Công ty tài chính (FI).
* **Assign Distribution Channel / Division to Sales Org:** Gắn kết chúng lại với nhau.
* **Set up Sales Area (`OVXG`):** Thiết lập "Khu vực bán hàng" là sự kết hợp của (Sales Org + Dist. Channel + Division). Đây là chìa khóa để chốt một đơn hàng SD.

---

## 2. Cấu hình Quy trình Bán hàng (Sales Documents)

**Đường dẫn:** `Sales and Distribution -> Sales -> Sales Documents -> Sales Document Header`

* **Define Sales Document Types (`VOV8`):** 
  * Định nghĩa các loại Đơn hàng (Ví dụ: `OR` - Standard Order, `RE` - Đơn hàng trả lại Return).
  * Quy định xem loại đơn hàng này có sinh ra phiếu Giao hàng (Delivery) hay Hóa đơn (Billing) tự động không.
* **Define Item Categories (`VOV7`):** 
  * Cấu hình từng dòng của Đơn hàng (Ví dụ: Dòng `TAN` - Hàng hóa bình thường có xuất kho tính tiền, `TANN` - Hàng tặng kèm Free of charge, không tính tiền).
* **Assign Item Categories (`VOV4`):** Gắn loại dòng (Item Category) vào loại đơn hàng (Document Type).

---

## 3. Trái tim của SD: Cấu hình Pricing (Thủ tục tính giá)

Đây là phần khó nhất và hay nhất của Consultant SD.
**Đường dẫn:** `Sales and Distribution -> Basic Functions -> Pricing -> Pricing Control`

* **Define Condition Types (Loại điều kiện - `V/06`):**
  * Tạo mã cho từng thành phần giá (Ví dụ: `PR00` - Giá gộp, `K004` - Chiết khấu, `MWST` - Thuế GTGT).
* **Define Pricing Procedure (Thủ tục tính giá - `V/08`):**
  * Xếp các Condition Type thành một công thức kéo dài từ trên xuống dưới.
  * *Ví dụ Step 10:* Lấy Giá gộp (PR00). *Step 20:* Lấy Chiết khấu (K004). *Step 30:* Tổng = Step 10 - Step 20. *Step 40:* Tính Thuế (MWST) = 10% của Step 30. *Step 50:* Giá trị cuối cùng khách phải trả = Step 30 + Step 40.
* **Pricing Procedure Determination (`OVKK`):** 
  * Dùng thuật toán để hệ thống tự biết Đơn hàng nào thì dùng Công thức tính giá nào (Dựa vào Sales Area + Nhóm khách hàng + Nhóm văn bản).

---

## 4. Tích hợp SD-FI (Tự động hạch toán doanh thu - VKOA)

Cũng giống như OBYC của MM, SD dùng **VKOA** để tự động gắn tài khoản doanh thu khi xuất hóa đơn.
**Đường dẫn:** `Sales and Distribution -> Basic Functions -> Account Assignment/Costing -> Revenue Account Determination -> Assign G/L Accounts (VKOA)`

* Ở đây Consultant sẽ map (ánh xạ):
  * Nếu Khách hàng nội địa + Mua vật tư A -> Hạch toán vào TK Doanh thu 5111.
  * Nếu Khách hàng quốc tế + Mua vật tư A -> Hạch toán vào TK Doanh thu xuất khẩu 5112.
