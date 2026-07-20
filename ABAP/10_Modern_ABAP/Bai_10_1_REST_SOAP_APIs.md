# 🌐 Bài 10.1: Tích hợp Hệ Thống - REST API và SOAP Web Services

**Mục tiêu:** Hiểu cơ chế giao tiếp giữa SAP và các hệ thống bên ngoài (Non-SAP) thông qua các tiêu chuẩn Web API phổ biến nhất: SOAP và REST.

---

## 1. Tổng Quan Về Tích Hợp (Integration)
Trong thực tế, SAP không đứng một mình. Doanh nghiệp thường dùng SAP làm lõi (Core ERP), nhưng dùng Salesforce để quản lý khách hàng (CRM), dùng Magento làm web bán lẻ, dùng hệ thống của ngân hàng để chuyển tiền. Do đó, các hệ thống này phải "nói chuyện" được với nhau.

SAP cung cấp các cơ chế gọi ra ngoài (Outbound) và cho phép bên ngoài gọi vào (Inbound) thông qua **SOAP** và **REST**.

---

## 2. SOAP Web Services (Giao thức cũ, bảo mật cao)
**SOAP (Simple Object Access Protocol)** sử dụng định dạng **XML** để truyền dữ liệu. Mọi thứ được định nghĩa chặt chẽ qua file **WSDL** (Web Services Description Language).

* **Inbound (Bên ngoài gọi vào SAP):**
  * Bạn viết một Function Module (RFC) trong SE37.
  * Dùng T-Code `SOAMANAGER` để "xuất bản" (publish) RFC đó thành một Web Service, lấy đường link WSDL đưa cho bên thứ 3.
* **Outbound (SAP gọi ra ngoài):**
  * Bên thứ 3 cung cấp cho bạn một file WSDL.
  * Trong SAP, dùng T-Code `SE80` -> Create Enterprise Service -> Consume Web Service (Đọc file WSDL). Hệ thống tự sinh ra các ABAP Proxy Classes. Bạn viết code gọi Class này để truyền dữ liệu đi.

* **Ưu điểm:** Cực kỳ chặt chẽ, bảo mật cao (WS-Security), thích hợp cho các giao dịch tài chính (Ngân hàng).
* **Nhược điểm:** XML nặng nề, khó đọc, tốc độ chậm.

---

## 3. REST API (Giao thức hiện đại, nhẹ nhàng)
**REST (Representational State Transfer)** sử dụng định dạng **JSON** (JavaScript Object Notation), nhẹ hơn XML rất nhiều. Đây là chuẩn giao tiếp số 1 của thế giới Web và Mobile hiện nay.

* **Trong SAP ECC (Cũ):** 
  * Không hỗ trợ REST một cách native. Bạn phải tự viết code thông qua các class như `CL_REST_HTTP_CLIENT` để gọi ra ngoài (Outbound).
  * Inbound thì phải dùng T-Code `SICF` tạo một HTTP Handler class (triển khai Interface `IF_HTTP_EXTENSION`), tự tay parse/sinh ra chuỗi JSON.
* **Trong SAP S/4HANA:** 
  * REST được hỗ trợ tận răng thông qua **OData** (chúng ta sẽ học ở Bài 10.2).

* **Ưu điểm:** Nhẹ, nhanh, dễ tích hợp với Web/App, JSON dễ đọc.
* **Nhược điểm:** Không quy chuẩn chặt chẽ như SOAP WSDL.
