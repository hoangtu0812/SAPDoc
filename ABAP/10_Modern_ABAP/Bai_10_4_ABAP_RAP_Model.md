# 🔮 Bài 10.4: ABAP RAP (RESTful Application Programming Model)

**Mục tiêu:** Nắm bắt tương lai của lập trình ABAP trên S/4HANA và SAP BTP (Business Technology Platform). 

---

## 1. Tại sao có RAP?
Trước đây, để làm ra một ứng dụng Fiori trọn vẹn, chúng ta phải làm khá nhiều bước thủ công:
1. Tạo bảng (SE11).
2. Viết OData qua SEGW (viết code CRUD mỏi tay).
3. Code SAP UI5 ở Frontend để vẽ giao diện.

SAP nhận ra quy trình này tốn quá nhiều thời gian. Vì vậy, SAP tung ra **RAP (ABAP RESTful Application Programming Model)**.
RAP giúp lập trình viên tự động hóa phần lớn quá trình tạo OData và tạo giao diện.

---

## 2. Kiến trúc của RAP
RAP hoạt động dựa trên 3 trụ cột (Pillars):

### Trụ cột 1: Data Model & Behavior (Mô hình Dữ liệu và Hành vi)
Được xây dựng 100% bằng **CDS Views (Core Data Services)** thay vì SE11.
* Bạn định nghĩa dữ liệu bằng CDS.
* **Behavior Definition (BDEF):** Khai báo các hành vi cho CDS đó (Ví dụ: Cho phép Create, Update, Delete, hoặc tạo chức năng Approve). Khung code (framework) sẽ tự động sinh ra mà bạn không cần gõ lệnh INSERT/UPDATE thủ công.

### Trụ cột 2: Business Service Provisioning (Cung cấp Dịch vụ)
Từ CDS View, bạn chỉ cần gõ vài dòng khai báo (Service Definition & Service Binding) là hệ thống **tự động biến CDS đó thành một OData Service** hoàn chỉnh (V2 hoặc V4) mà không cần phải dùng SEGW.

### Trụ cột 3: Service Consumption (Tiêu thụ Dịch vụ)
* OData Service được ném ra ngoài cho SAP Fiori Elements tự động vẽ giao diện dựa trên các *UI Annotations* (khai báo định dạng) gắn ngay trong CDS. Bạn gần như không cần code Javascript ở Frontend.

---

## 3. Lập trình RAP ở đâu?
Bạn **không thể** code RAP trên màn hình SAP GUI xanh truyền thống (SE38/SE11/SEGW). 
Bắt buộc phải sử dụng **Eclipse** đã cài plugin **ABAP Development Tools (ADT)**.

### Tóm tắt lợi ích của RAP:
* Rút ngắn thời gian lập trình từ vài ngày xuống vài giờ.
* Kiến trúc chuẩn hóa, dễ bảo trì, tối ưu hóa cực tốt cho cơ sở dữ liệu HANA.
* Là công nghệ "phải biết" nếu bạn muốn trở thành chuyên gia S/4HANA hoặc làm việc trên SAP BTP (Cloud).
