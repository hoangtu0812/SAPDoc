# ⚙️ Bài 3: Hướng Dẫn Cấu Hình Hệ Thống (SPRO) Phân Hệ CO

**Mục tiêu:** Cấu hình "bộ khung" của Kế toán quản trị (CO) để sẵn sàng nhận dữ liệu chi phí và phân tích lợi nhuận.

Sử dụng T-Code **`SPRO`** -> SAP Reference IMG.

---

## 1. Cấu trúc Doanh nghiệp (Enterprise Structure)

**Đường dẫn:** `Enterprise Structure -> Definition -> Controlling`
* **Maintain Controlling Area (`OX06`):** Khởi tạo vùng kiểm soát (Controlling Area - CO Area). Lưu ý:
  * Khai báo Fiscal Year Variant và Chart of Accounts bắt buộc **phải giống hệt** với bên FI để 2 bên nói chuyện được với nhau.

**Đường dẫn:** `Enterprise Structure -> Assignment -> Controlling`
* **Assign Company Code to Controlling Area (`OX19`):** Gắn mã công ty FI (VD: `VN01`) vào CO Area vừa tạo. (Nếu cấu hình đa quốc gia, có thể gắn nhiều Company Code vào 1 CO Area).

---

## 2. Cấu hình Chung của CO (Controlling General)

**Đường dẫn:** `Controlling -> General Controlling -> Organization`
* **Maintain Versions (`OKEQ`):** 
  * Cấu hình "Version 0" (Đây là version mặc định để lưu dữ liệu thực tế Actual và dữ liệu Kế hoạch Plan). Trong quá trình lập ngân sách, bạn có thể tạo Version 1, 2 để làm bản nháp.
* **Maintain Number Ranges for Controlling Documents (`KANK`):** Khi luân chuyển chi phí trong CO (Assessment, Distribution), CO cũng sinh ra chứng từ. Cần khai báo dải số cho chứng từ CO.

---

## 3. Cấu hình Trung tâm Chi phí (Cost Center Accounting)

**Đường dẫn:** `Controlling -> Cost Center Accounting -> Master Data -> Cost Centers`
* **Define Standard Hierarchy (`OKENN`):** Định nghĩa Cây phân cấp chuẩn. Bắt buộc phải có một "gốc" (Root Node) đại diện cho toàn công ty, sau đó chia thành các nhánh (Khối Sản xuất, Khối Văn phòng) và cuối cùng là các Cost Center lá.
* **Cost Center Categories:** Phân loại CC (Ví dụ: `W` - Phân xưởng sản xuất, `A` - Hành chính, `L` - Kho vận).

**Đường dẫn:** `Controlling -> Cost Center Accounting -> Actual Postings`
* **Allocation Structures:** Cấu hình bộ khung phân bổ chi phí. Gom nhóm các chi phí cần phân bổ (Ví dụ nhóm Tiền điện, nước, internet) để phân bổ hàng loạt xuống cấp dưới.

---

## 4. Cấu hình Trung tâm Lợi nhuận (Profit Center Accounting)

**Đường dẫn:** `Controlling -> Profit Center Accounting -> Basic Settings`
* **Set Control Parameters for Actual Data:** Kích hoạt tính năng hạch toán dữ liệu thực tế vào Profit Center. Trái tim của PCA.
* **Maintain Standard Hierarchy (Profit Center):** Giống Cost Center, Profit Center cũng cần một cây cấu trúc phân cấp riêng (Ví dụ: Khối Bán lẻ, Khối Bán sỉ, Khối Dịch vụ).
* **Dummy Profit Center:** SAP yêu cầu tạo một Profit Center "ảo" (Dummy). Nếu kế toán FI quên không gắn Profit Center khi hạch toán doanh thu, hệ thống sẽ nhét tạm số tiền đó vào Dummy PC để cuối tháng kế toán vào kiểm tra và sửa lại, tránh mất dữ liệu.
