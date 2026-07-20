# ⚡ Bài 12: Tối Ưu Hóa Hiệu Suất (Performance Tuning)

**Mục tiêu:** Giúp chương trình xử lý hàng triệu dòng dữ liệu một cách mượt mà, không bị nghẽn (Timeout/Dump) và tiết kiệm tài nguyên Server.

---

## 1. Tối ưu hóa Database (SQL Tuning) - Rất quan trọng!

Cổ chai (Bottleneck) lớn nhất của SAP thường nằm ở việc lấy dữ liệu từ Database. Dưới đây là các nguyên tắc "Sống còn":

### 🚫 Tuyệt đối không dùng SELECT bên trong vòng lặp (LOOP)
* 🔴 **Tệ:** Cứ mỗi lần lặp một bản ghi, hệ thống lại phải gửi 1 request xuống Database. Nếu LOOP 100,000 lần = 100,000 requests. Chương trình sẽ treo!
  ```abap
  LOOP AT lt_vbak INTO ls_vbak.
    SELECT SINGLE * FROM vbap INTO ls_vbap WHERE vbeln = ls_vbak-vbeln.
  ENDLOOP.
  ```
* 🟢 **Tốt:** Sử dụng `FOR ALL ENTRIES` hoặc `JOIN` để gom lại thành 1 cục và lấy 1 lần duy nhất trước khi LOOP.
  ```abap
  IF lt_vbak IS NOT INITIAL.
    SELECT vbeln, posnr, matnr 
      FROM vbap 
      INTO TABLE @DATA(lt_vbap)
      FOR ALL ENTRIES IN @lt_vbak
      WHERE vbeln = @lt_vbak-vbeln.
  ENDIF.
  ```
*(Lưu ý bắt buộc: Luôn kiểm tra mảng rỗng `IS NOT INITIAL` trước khi dùng `FOR ALL ENTRIES`, nếu không nó sẽ tải toàn bộ dữ liệu của bảng vbap).*

### 🚫 Tránh SELECT *
Chỉ lấy các trường (fields) bạn sẽ sử dụng. Việc tải 200 trường về RAM trong khi bạn chỉ dùng 2 trường là sự lãng phí khủng khiếp.

### 🚫 Sử dụng WHERE một cách khôn ngoan
Lọc dữ liệu tối đa ở cấp độ Database (trong mệnh đề `WHERE`), đừng tải toàn bộ bảng về Internal Table rồi mới dùng lệnh `DELETE` để lọc. Hãy để Database làm đúng nhiệm vụ của nó.
Luôn cung cấp Khóa chính (Primary Key) hoặc các trường có đánh Index trong mệnh đề `WHERE` nếu có thể.

---

## 2. Tối ưu hóa Internal Table (RAM & CPU)

### 🚀 Dùng ASSIGNING thay vì INTO (Field Symbols)
Khi lặp qua một Internal Table lớn để chỉnh sửa dữ liệu, đừng dùng `INTO ls_workarea`. Vì lệnh này sẽ *copy* từng dòng từ bảng sang Work Area -> Rất tốn RAM và thời gian.
Hãy dùng Field Symbol (`<fs>`). Nó chỉ trỏ địa chỉ vào vùng nhớ đó (Reference), tốc độ tăng gấp nhiều lần.
* 🟢 **Good:**
  ```abap
  LOOP AT lt_mara ASSIGNING FIELD-SYMBOL(<fs_mara>).
    <fs_mara>-mtart = 'FERT'. " Dữ liệu trong bảng lt_mara thay đổi ngay lập tức
  ENDLOOP.
  ```

### 🚀 Tìm kiếm tốc độ cao (BINARY SEARCH)
Lệnh `READ TABLE` mặc định sẽ dò từ trên xuống dưới (Linear Search). Nếu bảng có 1 triệu dòng, việc dò tìm rất lâu.
Hãy sắp xếp (`SORT`) bảng trước, sau đó dùng lệnh `BINARY SEARCH` để chia đôi mảng tìm kiếm, thuật toán này nhanh như chớp!
* 🟢 **Good:**
  ```abap
  SORT lt_mara BY matnr.
  READ TABLE lt_mara INTO DATA(ls_mara) WITH KEY matnr = '123' BINARY SEARCH.
  ```
*(Gợi ý: Trong ABAP hiện đại, hãy ưu tiên dùng `SORTED TABLE` hoặc `HASHED TABLE` thay vì `STANDARD TABLE` nếu cần đọc dữ liệu rất nhiều lần).*

---

## 3. Các Công cụ (Tools) để đo lường Performance
ABAP cung cấp sẵn các "máy đo" siêu xịn để bạn kiểm tra xem code của mình đang chạy chậm ở đâu:
* **ST05 (Performance Trace - SQL Trace):** Giúp bạn theo dõi câu lệnh SQL nào đang chạy, mất bao nhiêu miligiây, quét bao nhiêu dòng dữ liệu dưới Database, và có dùng Index hay không.
* **SAT (Runtime Analysis):** Phân tích chi tiết thời gian chạy của từng Subroutine, Method, Function Module trong chương trình, xem hàm nào ngốn CPU nhất.
* **Code Inspector (SCI) hoặc ATC (ABAP Test Cockpit):** Công cụ check lỗi tự động, nó sẽ tự động báo cho bạn biết nếu bạn lỡ viết một đoạn mã "Bad Coding" ảnh hưởng tới hiệu suất trước khi hệ thống cho phép đem qua môi trường thật.
