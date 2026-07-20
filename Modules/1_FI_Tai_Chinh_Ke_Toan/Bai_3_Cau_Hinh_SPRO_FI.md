# ⚙️ Bài 3: Hướng Dẫn Cấu Hình Hệ Thống (SPRO) Phân Hệ FI

**Mục tiêu:** Nắm bắt các bước cốt lõi để Consultant setup phân hệ Kế toán Tài chính (FI) cho một công ty mới tinh trong SAP (Roll-out hoặc Implementation).

Sử dụng T-Code **`SPRO`** -> SAP Reference IMG.

---

## 1. Cấu trúc Doanh nghiệp (Enterprise Structure)

**Đường dẫn:** `Enterprise Structure -> Definition -> Financial Accounting`
* **Define Company Code (`OX02`):** Bước đầu tiên, tạo một Mã Công ty 4 ký tự (VD: `VN01`). Điền thông tin tên công ty, quốc gia, loại tiền tệ (VND) và ngôn ngữ.

**Đường dẫn:** `Enterprise Structure -> Assignment -> Financial Accounting`
* **Assign Company Code to Company (`OX16`):** Gắn Company Code vào Company (Đơn vị tập đoàn) nếu có để hợp nhất báo cáo.

---

## 2. Các Cấu Hình Tài Chính Cốt Lõi (Financial Accounting Global Settings)

**Đường dẫn:** `Financial Accounting (New) -> Financial Accounting Global Settings (New)`

* **Fiscal Year Variant (Năm tài chính - `OB29`):** Ở Việt Nam thường là từ 01/01 đến 31/12 (Khai báo mã V3 hoặc K4). Hoặc các công ty Nhật Bản thường từ 01/04 đến 31/03 năm sau.
* **Posting Period Variant (Kỳ kế toán - `OBBO`):** Định nghĩa mã kỳ kế toán và gắn vào Company Code (`OBBP`). Sau này dùng `OB52` để Đóng/Mở sổ hàng tháng (Ví dụ: Qua tháng 2 thì đóng không cho hạch toán tháng 1 nữa).
* **Document Type & Number Range (`OBA7` & `FBN1`):** 
  * Cấu hình loại chứng từ (Ví dụ: `SA` cho G/L, `KR` cho Vendor Invoice).
  * Gắn dải số tự động (Number range) cho từng loại chứng từ (VD: Chứng từ SA bắt đầu từ 1000000, KR bắt đầu từ 5000000).
* **Field Status Variant (`OBC4`):** Quản lý màn hình nhập liệu lúc hạch toán kế toán. Quy định ô nào bắt buộc nhập, ô nào được ẩn đi (Ví dụ: Khi nhập chi phí, bắt buộc phải nhập ô Cost Center).

---

## 3. Hệ Thống Tài Khoản (General Ledger Accounting)

**Đường dẫn:** `Financial Accounting (New) -> General Ledger Accounting (New) -> Master Data -> G/L Accounts`

* **Chart of Accounts (Hệ thống TK - `OB13`):** Khai báo tên danh mục tài khoản chung. (VD: `VCOA` - VietNam Chart Of Account).
* **Assign Company Code to Chart of Accounts (`OB62`):** Gắn `VN01` sử dụng `VCOA`.
* **Account Groups (`OBD4`):** Chia nhóm các tài khoản (Ví dụ: Nhóm TK Tiền mặt 111*-112*, Nhóm TK Chi phí 6*). Khai báo độ dài mã tài khoản.
* **Retained Earnings Account (`OB53`):** Cực kỳ quan trọng! Cấu hình tài khoản Lợi nhuận chưa phân phối (TK 421). SAP cần biết tài khoản này để tự động kết chuyển số dư các tài khoản Lỗ/Lãi (Loại 5,6,7,8) vào cuối năm.

---

## 4. Thuế GTGT (Taxes on Sales/Purchases)

**Đường dẫn:** `Financial Accounting (New) -> Financial Accounting Global Settings (New) -> Tax on Sales/Purchases`

* **Tax Calculation Procedure:** Cấu hình công thức tính thuế của quốc gia (Khai báo phần trăm thuế).
* **Tax Codes (`FTXP`):** Khai báo các mã thuế (Ví dụ: `V1` = Thuế mua vào 10%, `V2` = Thuế bán ra 8%).
* **Automatic Tax Postings (`OB40`):** Gắn tài khoản (Ví dụ: TK 1331) vào mã thuế để hệ thống tự động sinh bút toán tiền thuế mà không cần kế toán phải tự gõ vào.
