# 🔌 Giai đoạn 8: Giao Tiếp và Chuyển Đổi Dữ Liệu (Interfaces)

**Mục tiêu:** Đưa dữ liệu hàng loạt từ bên ngoài (Excel, Text) vào SAP, hoặc thiết lập cầu nối giao tiếp dữ liệu giữa SAP và các hệ thống khác (Web, Mobile, 3rd party software).

---

## 📚 Kiến thức cần học
* 📼 **BDC (Batch Data Communication):** 
  * Dùng T-Code SHDB để ghi lại (record) thao tác người dùng trên màn hình.
  * Tự động hóa việc nhập liệu hàng loạt (Call Transaction hoặc Session Method).
* ⚙️ **BAPI (Business Application Programming Interface):** 
  * Các API chuẩn do SAP cung cấp (T-Code BAPI).
  * Cách gọi BAPI chuẩn xác (luôn gọi kèm `BAPI_TRANSACTION_COMMIT`).
* 📞 **RFC (Remote Function Call):** Hàm ABAP có thể được gọi từ một hệ thống bên ngoài hoặc từ một hệ thống SAP khác.
* 📨 **IDoc / ALE (Electronic Data Interchange):** Định dạng file chuẩn của SAP dùng để truyền tải chứng từ (như Đơn hàng) giữa các hệ thống (T-Code WE20, WE21...).

---

## 💻 Bài tập thực hành

### 📝 Bài tập: Import dữ liệu tự động bằng BDC
1. Mở T-Code SHDB, ghi lại quá trình tạo một Vật tư mới (Material) qua T-Code MM01.
2. Dựa vào code sinh ra từ SHDB, viết một chương trình ABAP:
   - Sử dụng Function Module `ALSM_EXCEL_TO_INTERNAL_TABLE` để đọc một file Excel từ máy tính người dùng (chứa 10 mã vật tư).
   - Loop qua dữ liệu Excel và dùng BDC chạy ngầm (`MODE = 'N'`) để tự động tạo 10 vật tư này trong SAP.
   - Trả về màn hình ALV báo cáo kết quả: Vật tư nào tạo thành công, vật tư nào bị lỗi.
