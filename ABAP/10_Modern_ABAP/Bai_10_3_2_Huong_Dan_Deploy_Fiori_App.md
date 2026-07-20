# 🚀 Bài 10.3.2: Hướng Dẫn End-to-End: Test, Deploy và Cấu hình Fiori Launchpad

Bạn đã code xong ứng dụng Fiori (SAP UI5) ở bài trước. Nhưng làm sao để đưa nó lên hệ thống SAP và xuất hiện trên màn hình Fiori Launchpad cho User bấm vào? Đây là quy trình "Từ đầu đến cuối" (End-to-End) dành cho bạn.

---

## 🛠️ Bước 1: Test ứng dụng ở môi trường Local (Máy cá nhân)
Trước khi đẩy code lên SAP, bạn phải chắc chắn code chạy đúng.
1. Mở Terminal trong VS Code (hoặc SAP Business Application Studio - BAS).
2. Chạy lệnh: `npm install` (Để tải các thư viện cần thiết).
3. Chạy lệnh: `npm run start` (Hoặc `ui5 serve`).
4. Hệ thống sẽ mở một trình duyệt ảo ở cổng `http://localhost:8080`. Bạn có thể click vào các nút, xem bảng dữ liệu xem nó đã gọi đúng OData từ hệ thống SAP chưa.

---

## ☁️ Bước 2: Deploy (Đẩy Code) Lên Máy Chủ SAP (ABAP Repository)
Ứng dụng UI5 thực chất là các file HTML, JS, XML. Để SAP chạy được nó, ta phải ném các file này vào một kho lưu trữ gọi là **SAP UI5 ABAP Repository**.

**Cách 1: Deploy bằng Command Line (Khuyên dùng cho VS Code/BAS)**
1. Chạy lệnh: `npm run deploy` (Lệnh này được cấu hình sẵn trong file `ui5-deploy.yaml`).
2. Hệ thống sẽ hỏi Username, Password SAP, Package (Ví dụ: `$TMP` hoặc `Z_PACKAGE`) và Transport Request.
3. Chờ 1 phút, code sẽ được nén và đẩy thẳng lên SAP.

**Cách 2: Deploy thủ công bằng SAP GUI (SE38)**
1. Chạy lệnh `npm run build` ở local. Nó sẽ tạo ra thư mục `dist` chứa code đã tối ưu.
2. Nén thư mục `dist` thành file `.zip`.
3. Vào SAP GUI, mở T-Code **`SE38`**. Chạy chương trình **`/UI5/UI5_REPOSITORY_LOAD`**.
4. Chọn file `.zip` vừa tạo, nhập tên ứng dụng (VD: `Z_MY_MATERIAL_APP`) và bấm Execute. Code đã nằm gọn trong SAP!

---

## 🔌 Bước 3: Kích Hoạt Dịch Vụ Web (T-Code SICF)
SAP cần một đường link HTTP để gọi được ứng dụng vừa đẩy lên.
1. Vào T-Code **`SICF`**.
2. Tìm theo đường dẫn: `default_host -> sap -> bc -> ui5_ui5 -> sap -> z_my_material_app`.
3. Chuột phải vào tên ứng dụng, chọn **Activate Service**.
*(Bây giờ bạn đã có thể chạy app trực tiếp qua một link URL dài ngoằng của hệ thống, nhưng User thì không nhớ nổi link đó. Ta phải đưa nó lên Fiori Launchpad).*

---

## 📱 Bước 4: Cấu Hình Fiori Launchpad (Tạo Tile / Ô Vuông)
Đây là công việc của Fiori Administrator. (Sử dụng T-Code **`/UI2/FLPCM_CUST`** - Fiori Launchpad Content Manager, bản mới của S/4HANA).

**1. Tạo Semantic Object (Đối tượng ngữ nghĩa)**
* Mở T-Code **`/UI2/SEMOBJ`**.
* Tạo một mã mới, ví dụ: `ZMaterial` (Đại diện cho ứng dụng quản lý vật tư của bạn).

**2. Tạo Target Mapping (Ánh xạ mục tiêu)**
* Vào FLP Content Manager, tạo một Target Mapping.
* Khai báo: 
  * Semantic Object: `ZMaterial`
  * Action: `display` (Hành động là Xem).
  * Application Type: `SAP UI5 Fiori App`.
  * URL: Kéo link từ Bước 3 vào.
  * Component ID: Nhập tên ID trong file `manifest.json` của app (Ví dụ: `my.company.app`).

**3. Tạo Tile (Cái ô vuông để User bấm vào)**
* Tạo một Tile, nhập Tiêu đề: "Quản Lý Vật Tư".
* Gắn Semantic Object `ZMaterial` và Action `display` vào Tile này.
* Gắn Tile và Target Mapping vào một **Catalog** (Ví dụ: `Z_CATALOG_KHO`).

---

## 🔐 Bước 5: Phân Quyền Cho User Bằng PFCG
User sẽ không thấy cái Tile "Quản Lý Vật Tư" nếu họ không có quyền.
1. Mở T-Code **`PFCG`**.
2. Tạo một Role mới (Ví dụ: `Z_ROLE_THU_KHO`).
3. Chuyển sang tab **Menu**, chọn thêm **SAP Fiori Tile Catalog**. Nhập tên Catalog `Z_CATALOG_KHO` ở Bước 4 vào.
4. Lưu và **Generate Profile**.
5. Sang tab **User**, gán User ID của người thủ kho vào Role này.

---

## 🎉 Bước 6: Test Chạy Thực Tế (End-to-End)
1. Đăng nhập vào đường link Fiori Launchpad của công ty bằng tài khoản Thủ Kho.
2. Màn hình chính hiện ra ô vuông "Quản Lý Vật Tư" cực kỳ đẹp mắt.
3. Click vào ô vuông.
4. Giao diện tải lên, vòng tròn xoay xoay (Loading), sau đó gửi lệnh GET xuống OData SEGW, OData chọc xuống bảng MARA, lấy data ném lên Fiori, và bảng Danh sách Vật tư hiện ra. 
**Thành công 100%!**
