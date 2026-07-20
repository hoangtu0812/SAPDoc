# 📱 Bài 10.3: SAP UI5 và SAP Fiori

**Mục tiêu:** Hiểu về giao diện người dùng thế hệ mới của SAP, thay thế cho màn hình SAP GUI xám xịt truyền thống.

---

## 1. Sự khác biệt giữa SAP UI5 và SAP Fiori

Rất nhiều người nhầm lẫn 2 khái niệm này. Thực chất:
* **SAP UI5 (User Interface 5):** Là một thư viện/framework lập trình frontend (giống như ReactJS, Angular, VueJS) dựa trên HTML5, CSS và JavaScript. Nó được SAP tạo ra để vẽ nên các nút bấm, bảng biểu, đồ thị.
* **SAP Fiori:** Không phải là một ngôn ngữ lập trình. Nó là **Bộ nguyên tắc thiết kế (Design Guidelines)**. Fiori quy định các ứng dụng phải nhất quán về giao diện, màu sắc, trải nghiệm người dùng (UX) và có thể chạy mượt mà trên cả Điện thoại, Máy tính bảng và Desktop (Responsive).

=> *Tóm lại:* Bạn dùng **ngôn ngữ SAP UI5** để lập trình ra các **ứng dụng chuẩn Fiori**.

---

## 2. Kiến trúc của một ứng dụng Fiori
Một ứng dụng Fiori tiêu chuẩn hoạt động theo kiến trúc 3 lớp (MVC - Model View Controller):
* **View (XML):** Giao diện hiển thị. Trong SAP UI5, View thường được viết bằng XML. Nơi đây chứa định nghĩa nút bấm, bảng biểu.
* **Controller (JavaScript):** Nơi chứa logic chạy trên trình duyệt (Browser). Xử lý sự kiện click chuột, validate dữ liệu đầu vào.
* **Model (JSON/OData):** Dữ liệu của ứng dụng.
  * Ứng dụng Fiori không tự chứa dữ liệu. Nó gọi các **OData Services** (từ backend ABAP - Bài 10.2) thông qua mạng Internet/Intranet để kéo dữ liệu JSON về và gài (bind) lên View.

---

## 3. Fiori Launchpad (Lối vào)
Thay vì dùng T-Code như trước, người dùng hiện đại truy cập SAP qua một trang web duy nhất gọi là **Fiori Launchpad**. 
Trên đó chứa các "Tiles" (Ô vuông). Mỗi Tile tương ứng với một ứng dụng (Ví dụ: Ứng dụng "Duyệt Đơn hàng", Ứng dụng "Khai báo ngày nghỉ").

## 4. Công cụ lập trình
Frontend Developer không dùng `SE38` để code SAP UI5. Họ sử dụng:
* **SAP Business Application Studio (BAS):** Nền tảng cloud trên SAP BTP.
* **VS Code (Visual Studio Code):** Cài đặt Fiori tools extension để code offline tại máy tính.
