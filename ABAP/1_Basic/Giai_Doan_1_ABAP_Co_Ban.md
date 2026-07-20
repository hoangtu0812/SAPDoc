# 🚀 Giai đoạn 1: ABAP Cơ Bản (ABAP Fundamentals)

**Mục tiêu:** Làm quen với môi trường code và các cú pháp cơ bản nhất trong SAP.

---

## 📚 Kiến thức cần học
* 🏢 **Giới thiệu về SAP:** Kiến trúc 3 lớp (Presentation, Application, Database).
* ⌨️ **T-Code SE38:** Cách sử dụng ABAP Editor để viết chương trình đầu tiên.
* 📦 **Các kiểu dữ liệu cơ bản:** 
  * `C` (Character)
  * `N` (Numeric Text)
  * `I` (Integer)
  * `P` (Packed - Số thập phân)
  * `D` (Date), `T` (Time), `String`.
* 🏷️ **Khai báo:** Biến (`DATA`), hằng số (`CONSTANTS`).
* 🔀 **Cấu trúc điều kiện:** `IF...ELSEIF...ELSE`, `CASE...WHEN`.
* 🔄 **Vòng lặp:** `DO...ENDDO`, `WHILE...ENDWHILE`.
* ✂️ **Xử lý chuỗi (String operations):** `CONCATENATE`, `SPLIT`, `REPLACE`, `CONDENSE`.

---

## 💻 Bài tập thực hành

### 📝 Bài tập 1: Máy tính bỏ túi
Viết chương trình máy tính bỏ túi (Cộng, trừ, nhân, chia) nhận đầu vào từ màn hình.
- Gợi ý: Sử dụng `PARAMETERS` để nhận 2 số nguyên và 1 phép tính (ví dụ: '+', '-', '*', '/').
- Dùng cấu trúc `CASE...WHEN` để kiểm tra phép tính và dùng lệnh `WRITE` để in kết quả ra màn hình.

### 📝 Bài tập 2: Bảng cửu chương
Viết chương trình in ra bảng cửu chương (từ 2 đến 9).
- Gợi ý: Sử dụng vòng lặp lồng nhau (`DO 8 TIMES` và `DO 10 TIMES`).
