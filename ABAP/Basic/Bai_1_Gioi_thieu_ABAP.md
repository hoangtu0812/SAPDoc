# Bài 1: Làm quen với ABAP - Ngôn ngữ "linh hồn" của SAP

Chào mừng bạn đến với bài học đầu tiên về ABAP! Trong bài này, chúng ta sẽ tìm hiểu ABAP là gì một cách đơn giản nhất và thử viết chương trình đầu tiên nhé.

---

## 1. ABAP là gì?

**ABAP** (đọc là a-báp) viết tắt của **Advanced Business Application Programming**. 

Hãy tưởng tượng hệ thống SAP ERP giống như một chiếc điện thoại thông minh khổng lồ dành cho doanh nghiệp. Trên chiếc điện thoại đó có sẵn rất nhiều ứng dụng (Kế toán, Mua hàng, Bán hàng...). 
- Nếu bạn muốn thay đổi cách một ứng dụng hoạt động, hoặc muốn tạo ra một ứng dụng hoàn toàn mới cho riêng công ty mình, bạn sẽ cần một ngôn ngữ lập trình.
- Và **ABAP** chính là ngôn ngữ lập trình đó! Nó được tạo ra bởi chính công ty SAP.

**Tóm lại:** ABAP là ngôn ngữ dùng để lập trình, chỉnh sửa và mở rộng các tính năng bên trong hệ thống phần mềm SAP.

---

## 2. Những "luật rừng" cơ bản của ABAP

Trước khi code, bạn chỉ cần nhớ 3 quy tắc "vàng" này:

1. **Luôn kết thúc bằng dấu chấm (`.`):** Giống như một câu văn kết thúc bằng dấu chấm, mỗi dòng lệnh trong ABAP bắt buộc phải kết thúc bằng một dấu chấm. Nếu quên, hệ thống sẽ báo lỗi ngay!
2. **Không phân biệt chữ hoa, chữ thường:** Chữ `WRITE`, `write`, hay `WrItE` đều giống nhau đối với ABAP. (Tuy nhiên, để code đẹp và dễ đọc, người ta thường viết hoa các từ khóa lệnh).
3. **Dấu nháy đơn (`'`) dùng cho chuỗi:** Nếu bạn muốn in ra một dòng chữ, hãy bỏ nó vào trong cặp dấu nháy đơn. Ví dụ: `'Xin chào'`.

---

## 3. Chạy chương trình ABAP ở đâu? (Khái niệm T-Code)

Khác với các ngôn ngữ khác (bạn phải tải Visual Studio, Eclipse...), code ABAP được viết **trực tiếp ngay bên trong phần mềm SAP**.

Để đi đến màn hình viết code, chúng ta sử dụng một khái niệm gọi là **T-Code** (Transaction Code - Mã giao dịch). Nó giống như một "đường tắt" để mở một màn hình chức năng.

👉 **T-Code thần thánh để viết code là:** `SE38` (ABAP Editor).
Chỉ cần gõ `SE38` vào ô tìm kiếm trên SAP, nhấn Enter, bạn sẽ vào được trình soạn thảo code.

---

## 4. Chương trình đầu tiên: "Hello World"

Giờ chúng ta hãy viết một chương trình đơn giản nhất: In ra màn hình dòng chữ "Xin chào ABAP!".

Khi bạn tạo một chương trình mới trong `SE38`, SAP sẽ tự tạo sẵn cho bạn một dòng khai báo tên chương trình như sau:

```abap
REPORT z_hello_world.
```

*(Lưu ý: Tên các chương trình do bạn tự tạo luôn phải bắt đầu bằng chữ `Z` hoặc `Y` để phân biệt với code gốc của SAP).*

Bây giờ, chúng ta sẽ thêm lệnh `WRITE` để in ra màn hình. Lệnh này giống hệt như lệnh `print` trong Python hay C.

```abap
REPORT z_hello_world.

* Đây là một dòng ghi chú (Comment) - Bắt đầu bằng dấu sao (*)

WRITE 'Xin chao ABAP! Day la chuong trinh dau tien cua toi.'.
```

**Giải thích:**
- `REPORT`: Khai báo đây là một chương trình báo cáo/thực thi.
- Dấu `*` ở đầu dòng dùng để viết ghi chú (comment). SAP sẽ bỏ qua dòng này khi chạy.
- `WRITE`: Lệnh in chữ ra màn hình.
- Chú ý **dấu chấm (`.`)** ở cuối các dòng lệnh!

---

## 5. Tổng kết Bài 1

- **ABAP** là ngôn ngữ lập trình của hệ thống SAP.
- Dùng **T-Code `SE38`** để mở màn hình viết code.
- Mỗi câu lệnh ABAP kết thúc bằng **dấu chấm (`.`)**.
- Lệnh **`WRITE`** dùng để in văn bản ra màn hình.
- Các chương trình tự viết phải bắt đầu bằng chữ **`Z`** hoặc **`Y`**.

Rất đơn giản phải không? Hẹn gặp bạn ở Bài 2, chúng ta sẽ học về cách tạo biến và tính toán nhé!

