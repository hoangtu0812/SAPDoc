# 📈 Bài 1: Tổng Quan Phân Hệ CO (Controlling)

**Mục tiêu:** Nắm bắt khái niệm Kế toán quản trị (CO) và cách nó giúp Ban giám đốc ra quyết định chiến lược.

---

## 📚 1. Phân hệ CO là gì?
Nếu **FI (Kế toán tài chính)** tập trung vào việc báo cáo ra **bên ngoài** doanh nghiệp, thì **SAP CO (Controlling - Kế toán quản trị)** sinh ra để phục vụ cho nhu cầu quản trị **nội bộ**.

CO cung cấp các thông tin và báo cáo giúp Ban lãnh đạo (BOD) theo dõi chặt chẽ chi phí, phân tích lợi nhuận của từng phòng ban, từng dự án hay từng dòng sản phẩm để từ đó đưa ra các quyết định cắt giảm chi phí hoặc tối ưu hóa hoạt động kinh doanh.

## 🏢 2. Các thành phần chính (Sub-modules)
Phân hệ CO gồm nhiều mảng tính toán phức tạp:
* **CCA (Cost Center Accounting - Kế toán trung tâm chi phí):** Theo dõi và quản lý các chi phí gián tiếp (Overhead costs) phát sinh tại các phòng ban (VD: Chi phí văn phòng phẩm phòng Nhân sự).
* **PCA (Profit Center Accounting - Kế toán trung tâm lợi nhuận):** Đánh giá hiệu quả (Doanh thu - Chi phí) của từng khu vực, phòng ban độc lập.
* **PC (Product Costing - Tính giá thành sản phẩm):** Tính toán giá trị cấu thành nên một sản phẩm sản xuất ra (bao gồm nguyên vật liệu, nhân công, chi phí máy móc). Cực kỳ quan trọng trong ngành sản xuất.
* **PA (Profitability Analysis - Phân tích khả năng sinh lời):** Phân tích lợi nhuận đa chiều (theo nhóm khách hàng, khu vực địa lý, kênh phân phối...).
* **IO (Internal Orders - Đơn hàng nội bộ):** Dùng để theo dõi chi phí cho các sự kiện/chiến dịch ngắn hạn (VD: Chi phí tổ chức Hội nghị khách hàng, Chi phí xây dựng một bãi đỗ xe mới).

## ⌨️ 3. Các Transaction Code (T-Code) Phổ Biến
* `KS01` / `KS02` / `KS03`: Tạo / Sửa / Xem Cost Center.
* `KB11N`: Điều chuyển chi phí thủ công (Manual Reposting of Costs).
* `CK11N`: Chạy tính toán giá thành định mức của vật tư (Create Material Cost Estimate).
* `CK24`: Cập nhật giá thành tiêu chuẩn vào Master Data (Price Update).
* `KSB1`: Xem báo cáo chi tiết các chi phí phát sinh trong một Cost Center.

## 🔗 4. Tích hợp (Integration)
* **CO và FI:** Gần như mọi bút toán chi phí hoặc doanh thu (P&L) bên FI khi hạch toán đều bắt buộc phải nhập một đối tượng chịu phí bên CO (như Cost Center, Internal Order) để phân bổ chi phí.
* **CO và PP (Production Planning):** Tính toán chi phí sản xuất (Product Costing) lấy dữ liệu Định mức vật tư (BOM) và Lộ trình sản xuất (Routing) từ phân hệ PP.
