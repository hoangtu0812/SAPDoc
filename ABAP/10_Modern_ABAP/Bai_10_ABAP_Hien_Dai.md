# 🚀 Bài 10: ABAP Hiện Đại (Modern ABAP & S/4HANA)

**Mục tiêu:** Bắt kịp các công nghệ mới nhất của SAP trong kỷ nguyên S/4HANA, nơi dữ liệu được xử lý trực tiếp dưới Database (HANA) và giao diện được chuyển dịch lên Web (Fiori).

---

## 📚 Kiến thức cần học
* ✨ **ABAP 7.4+ Syntax (Cú pháp mới):** 
  * Khai báo nội tuyến (Inline declarations: `DATA(lv_matnr) = ...`).
  * Table expressions.
  * Các toán tử mới như `NEW`, `VALUE`, `CORRESPONDING`, `REDUCE`, `FILTER`.
* ⚡ **Code-to-Data Paradigm:** Dịch chuyển logic từ Application Layer xuống Database Layer để tận dụng tốc độ của HANA.
* 👁️ **CDS Views (Core Data Services):** 
  * Cách định nghĩa View ngay tại mức Database bằng Eclipse / HANA Studio (thay thế dần các View trong SE11).
  * Xử lý join, tính toán trực tiếp trên CDS.
* 🏃‍♂️ **AMDP (ABAP Managed Database Procedures):** Viết SQLScript của HANA trực tiếp vào trong Class ABAP.
* 🌐 **OData Services (T-Code SEGW):** Xây dựng các API chuẩn RESTful để cung cấp dữ liệu cho ứng dụng Web.
* 📱 **SAP Fiori (Cơ bản):** Hiểu kiến trúc cơ bản và cách một ứng dụng Fiori UI5 gọi OData từ backend ABAP.

---

## 💻 Ứng dụng thực tế

Với các công nghệ này, lập trình viên ABAP hiện đại không chỉ làm việc trên màn hình SAP GUI cũ kỹ (SE38), mà sẽ cài đặt công cụ **ABAP Development Tools (ADT)** trên Eclipse để lập trình. 
Đây là hướng đi bắt buộc để nâng cấp bản thân và làm việc trong các dự án SAP S/4HANA cao cấp nhất.

