# 🚀 Bài 10.2.2: Hướng Dẫn End-to-End: Generate, Kích Hoạt và Test OData Service

Viết code trong DPC_EXT mới chỉ là 50% công việc. Để Fiori hoặc Postman có thể gọi được API này, bạn phải trải qua quy trình Kích hoạt (Registration) nghiêm ngặt của SAP Gateway. Dưới đây là các bước từ lúc gõ code xong đến lúc test ra kết quả.

---

## 🛠️ Bước 1: Generate Runtime Objects (Trong SEGW)
Sau khi thiết kế Entity và viết code xong, bạn phải để SAP tự sinh ra các class chạy ngầm.
1. Mở T-Code **`SEGW`**, mở Project của bạn (VD: `Z_MATERIAL_PROJ`).
2. Nhấn vào biểu tượng **Generate Runtime Objects** (Nút hình vòng tròn đỏ/trắng trên thanh công cụ).
3. Một bảng hiện ra liệt kê 4 class sẽ được tạo (MPC, MPC_EXT, DPC, DPC_EXT) và 1 Model Provider. Nhấn **Enter** để xác nhận.
4. Lưu vào một Transport Request. Cột thông báo góc dưới sẽ báo "Generated Successfully".

---

## 🔌 Bước 2: Đăng Ký Service (Trong /IWFND/MAINT_SERVICE)
Bây giờ, Service của bạn mới chỉ tồn tại ở dạng Code. Bạn phải "mở cổng" để biến nó thành một đường link HTTP.
1. Mở T-Code **`/IWFND/MAINT_SERVICE`** (Cửa sổ quản lý toàn bộ OData của SAP).
2. Bấm nút **Add Service** ở thanh công cụ phía trên.
3. Ở ô **System Alias**, gõ chữ `LOCAL` (Nếu hệ thống Gateway và ERP chung 1 server).
4. Ở ô **Technical Service Name**, gõ tên Project của bạn có đuôi `_SRV` (Ví dụ: `Z_MATERIAL_PROJ_SRV`), rồi nhấn **Enter** (Hoặc bấm nút Get Services).
5. Hệ thống sẽ tìm thấy Service của bạn ở danh sách bên dưới. Kích đúp (Double-click) vào nó.
6. Một cửa sổ Pop-up hiện ra. Đảm bảo ô **Package** được điền (Ví dụ `$TMP`). Nhấn OK.
7. Thông báo "Service was created and its metadata was loaded successfully" hiện ra.

---

## 🧪 Bước 3: Test API Nội Bộ (Trong /IWFND/GW_CLIENT)
SAP cung cấp sẵn một công cụ (giống hệt Postman nhưng nằm ngay trong SAP) để test OData.
1. Quay lại màn hình `/IWFND/MAINT_SERVICE`, tìm Service `Z_MATERIAL_PROJ_SRV` ở danh sách bên dưới và click chọn nó.
2. Nhấn nút **SAP Gateway Client** ở thanh công cụ bên dưới. (Hoặc gõ T-Code `/IWFND/GW_CLIENT`).
3. Màn hình test hiện ra. Ô **Request URI** đã được tự động điền sẵn link (Ví dụ: `/sap/opu/odata/sap/Z_MATERIAL_PROJ_SRV/?$format=json`).
4. Nhấn nút **Execute** (Hoặc F8).
5. Nếu cấu hình đúng, hệ thống trả về mã **HTTP Response 200** và một đoạn mã XML/JSON liệt kê các Collection (EntitySet) có trong Service.

**Test lấy dữ liệu chi tiết:**
1. Thêm tên EntitySet vào cuối URL: `/sap/opu/odata/sap/Z_MATERIAL_PROJ_SRV/MaterialSet?$format=json`.
2. Bấm **Execute**.
3. Góc bên phải (HTTP Response) sẽ hiển thị khối dữ liệu JSON chứa danh sách Vật tư mà bạn đã SELECT từ bảng MARA trong class `DPC_EXT`.

---

## 🌐 Bước 4: Test Bằng Công Cụ Bên Ngoài (Postman)
Để chứng minh API đã sẵn sàng cho Fiori hoặc Mobile App:
1. Copy đường link URL đầy đủ. (Thêm Hostname và Port của SAP, ví dụ: `http://saperp.company.com:8000/sap/opu/odata/sap/Z_MATERIAL_PROJ_SRV/MaterialSet?$format=json`).
2. Mở **Postman**.
3. Chọn Method: **GET**. Dán URL vào.
4. Chuyển sang Tab **Authorization** -> Chọn **Basic Auth** -> Nhập Username / Password đăng nhập SAP của bạn.
5. Bấm **Send**. Dữ liệu JSON sẽ trả về y hệt như bước 3.

---

## 🧹 Bước 5: Xử Lý Lỗi Cache (Kinh Nghiệm Sương Máu)
Khi làm việc với OData, có một lỗi cực kỳ khó chịu: Bạn sửa code ABAP, sửa Entity trong SEGW, nhưng ra ngoài Postman test thì dữ liệu KHÔNG ĐỔI. Lý do là SAP Gateway lưu Cache rất chặt.

**Quy trình chuẩn khi có thay đổi:**
Mỗi lần sửa cấu trúc OData, bạn PHẢI chạy 3 lệnh này để xóa Cache:
1. T-Code **`/IWFND/CACHE_CLEANUP`**: Xóa Cache ở tầng Gateway.
2. T-Code **`/IWBEP/CACHE_CLEANUP`**: Xóa Cache ở tầng Backend.
3. T-Code **`SMICM`** -> GoTo -> HTTP Plug-in -> Server Cache -> Invalidate Globally.

Làm xong 3 bước trên, OData của bạn sẽ nhận code mới 100%.
