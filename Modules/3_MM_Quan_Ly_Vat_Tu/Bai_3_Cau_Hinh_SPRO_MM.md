# ⚙️ Bài 3: Hướng Dẫn Cấu Hình Hệ Thống (SPRO) Phân Hệ MM

**Mục tiêu:** Thiết lập các thông số Master Data và quy trình Mua sắm (P2P), Quản lý Kho cho MM.

Sử dụng T-Code **`SPRO`** -> SAP Reference IMG.

---

## 1. Cấu trúc Doanh nghiệp (Enterprise Structure)

**Đường dẫn:** `Enterprise Structure -> Definition -> Materials Management`
* **Maintain Plant (`OX10`):** Tạo Nhà máy (Plant). Khai báo tên, địa chỉ, mã lịch làm việc (Factory Calendar).
* **Maintain Storage Location (`OX09`):** Tạo các kho vật lý nằm dưới Nhà máy (Ví dụ: Nhập Plant `VN01`, tạo các kho `K001` - Nguyên vật liệu, `K002` - Thành phẩm).
* **Maintain Purchasing Organization (`OX08`):** Tạo Tổ chức mua hàng.

**Đường dẫn:** `Enterprise Structure -> Assignment -> Materials Management`
* **Assign Plant to Company Code (`OX18`):** Gắn nhà máy MM về mã công ty bên FI.
* **Assign Purchasing Org to Company Code & Plant (`OX01` & `OX17`):** Định nghĩa xem tổ chức mua hàng này mua tập trung cho toàn công ty hay mua riêng cho từng nhà máy.

---

## 2. Cấu hình Dữ liệu chủ Vật tư (Material Master)

**Đường dẫn:** `Logistics - General -> Material Master -> Basic Settings`
* **Material Types (Loại vật tư - `OMS2`):** 
  * Cấu hình cực kỳ quan trọng! Tách biệt hàng mua ngoài (ROH - Nguyên liệu), Hàng tự sản xuất (FERT - Thành phẩm). 
  * Định nghĩa loại vật tư này có quản lý Tồn kho (Quantity update) và quản lý Giá trị (Value update) hay không. (VD: Văn phòng phẩm thường có Quantity nhưng không quản lý Value trong kho).
* **Number Ranges (`MMNR`):** Thiết lập Vật tư ROH thì mã tự động nhảy từ `1000000`, Vật tư FERT thì bắt đầu bằng `F-0001` (Gõ tay).

---

## 3. Cấu hình Quy trình Mua hàng (Purchasing)

**Đường dẫn:** `Materials Management -> Purchasing`
* **Document Types for PR / PO:** 
  * Định nghĩa các loại Đơn mua hàng (Ví dụ: `NB` - Standard PO, `UB` - Stock Transport Order).
  * Gắn Number Range cho Đơn đặt hàng.
* **Release Strategy (Chiến lược phê duyệt):**
  * Thiết lập luồng ký duyệt Đơn hàng (Cấu hình rất phức tạp). 
  * Ví dụ: PR giá trị < 10 triệu -> Trưởng phòng duyệt. PR > 10 triệu -> Giám đốc duyệt. Cấu hình bằng cách tạo Đặc tính (Characteristics) và Nhóm (Classes).

---

## 4. Tích hợp MM-FI (Tự động hạch toán - OBYC)

Đây là chỗ MM kết nối với Kế toán! Khi bạn làm lệnh kho, SAP không bắt bạn gõ Tài khoản kế toán. Nó tự suy ra nhờ cấu hình `OBYC`.

**Đường dẫn:** `Materials Management -> Valuation and Account Assignment -> Account Determination -> Account Determination Without Wizard -> Configure Automatic Postings (OBYC)`

* **Transaction Key (Mã giao dịch):**
  * **`BSX` (Inventory Posting):** Cấu hình tài khoản Hàng tồn kho (TK 152, 156).
  * **`WRX` (GR/IR Clearing):** Cấu hình tài khoản Trung gian Nhập kho/Hóa đơn (TK 3311).
  * **`GBB` (Offsetting entry):** Cấu hình các tài khoản Chi phí khi xuất kho (Ví dụ: Xuất kho vào sản xuất -> Ghi vào TK Chi phí nguyên vật liệu).
  * Consultant phải gắn đúng Valuation Class của vật tư vào đúng tài khoản Sổ cái G/L.
