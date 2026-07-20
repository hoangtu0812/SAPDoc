# ⚙️ Bài 3: Hướng Dẫn Cấu Hình Hệ Thống (SPRO) Phân Hệ PM

**Mục tiêu:** Hiểu cách Consultant cấu hình phân hệ Bảo trì (PM) để phù hợp với đặc thù vận hành của từng nhà máy.

Tất cả cấu hình được thực hiện thông qua T-Code **`SPRO`** -> SAP Reference IMG.

---

## 1. Định nghĩa Cấu trúc Nhà máy (Enterprise Structure)

**Đường dẫn:** `Enterprise Structure -> Definition -> Plant Maintenance`
* **Maintain Maintenance Planning Plant:** Nơi tập trung phòng ban lên kế hoạch bảo trì. Nếu công ty có 5 nhà máy nhưng chỉ có 1 phòng kỹ thuật trung tâm thì khai báo Planning Plant là nhà máy trung tâm đó.

**Đường dẫn:** `Enterprise Structure -> Assignment -> Plant Maintenance`
* **Assign Maintenance Planning Plant to Maintenance Plant:** Gắn nhà máy cần bảo trì vào nhà máy chịu trách nhiệm lên kế hoạch.

---

## 2. Cấu hình Dữ liệu chủ (Master Data)

**Đường dẫn:** `Plant Maintenance and Customer Service -> Master Data in Plant Maintenance and Customer Service`

* **Functional Locations (Vị trí lắp đặt):** 
  * Cấu hình **Structure Indicator (Mã cấu trúc):** Rất quan trọng! Quy định cách đặt tên cây thư mục nhà máy (Ví dụ định dạng `XXX-XX-XX` tương ứng `Nhà máy - Khu vực - Hệ thống`).

* **Equipment (Thiết bị):**
  * Định nghĩa **Equipment Category (Loại thiết bị):** Phân biệt giữa Xe cộ, Máy bơm, Tủ điện. Mỗi Category có thể có giao diện (màn hình nhập liệu) khác nhau.
  * Cấu hình đánh số tự động (Number Ranges) cho thiết bị.

---

## 3. Cấu hình Báo hỏng và Lệnh bảo trì (Notification & Order)

**Đường dẫn:** `Plant Maintenance and Customer Service -> Maintenance and Service Processing`

* **Notification Types (Loại thông báo):** 
  * Cấu hình các loại như `M1` (Bảo trì đột xuất), `M2` (Bảo trì phòng ngừa).
  * Định nghĩa màn hình nhập liệu: Cần hiển thị ô mã lỗi, nguyên nhân, hay hướng khắc phục không.
* **Order Types (Loại Đơn hàng):**
  * Cấu hình các loại Order `PM01` (Sửa chữa breakdown), `PM02` (Sửa chữa định kỳ), `PM03` (Thuê ngoài sửa).
  * **Settlement Profile:** Gắn cấu hình tài chính để quy định Order này chi phí sẽ trút về Cost Center hay trút về Dự án.
