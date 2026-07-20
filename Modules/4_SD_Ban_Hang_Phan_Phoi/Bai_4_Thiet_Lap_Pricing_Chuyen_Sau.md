# 🛒 Bài 4: Chuyên Sâu Về Thủ Tục Tính Giá (Pricing Procedure) Của SD

Trong SD, bộ máy tính giá (Pricing Engine) sử dụng kỹ thuật gọi là **Condition Technique** (Kỹ thuật điều kiện). Đây là hệ thống logic mạnh mẽ và linh hoạt nhất của SAP, cho phép đáp ứng mọi chiến lược giá "điên rồ" nhất của đội Marketing.

---

## 1. Thành phần của Kỹ thuật điều kiện (Condition Technique)

Hãy đi từ dưới lên trên để hiểu cách hệ thống tìm ra được mức giá cuối cùng:

### 1. Condition Table (Bảng điều kiện) - `V/03`
Xác định "Tổ hợp khóa" để tìm giá. 
Ví dụ: Marketing ra luật "Khách hàng VIP mua Sản phẩm A thì được giảm giá". Tổ hợp khóa ở đây là `Khách hàng + Vật tư`.

### 2. Access Sequence (Chuỗi truy cập) - `V/07`
Một điều kiện giá có thể có nhiều tổ hợp khóa ưu tiên. 
* Ví dụ tìm giá giảm: Hệ thống sẽ quét bảng số 1 (Khách VIP + Vật tư A) trước. Nếu không có cài đặt giá, nó sẽ nhảy xuống tìm bảng số 2 (Chỉ lọc theo Vật tư A chung chung).
Chuỗi truy cập định nghĩa thứ tự tìm kiếm này. Tìm thấy giá ở đâu thì lấy luôn và dừng lại (Exclusive).

### 3. Condition Type (Loại điều kiện) - `V/06`
Loại giá trị bạn đang tính là gì?
* **PR00:** Giá gốc (Price) - Thường tính bằng $ / 1 sản phẩm.
* **K004:** Chiết khấu (Discount) - Có thể tính bằng % hoặc trừ thẳng tiền mặt.
* **KF00:** Cước phí vận chuyển (Freight) - Tính theo Trọng lượng (Kg).
* **MWST:** Thuế (Tax) - Tính % dựa trên tổng tiền.

### 4. Pricing Procedure (Thủ tục tính giá) - `V/08`
Khung sườn gom các Condition Type lại thành công thức toán học.

---

## 2. Giải Phẫu Màn Hình Pricing Procedure (V/08)

Khi bạn cấu hình Pricing Procedure, bạn sẽ thấy một bảng với các cột cực kỳ quan trọng:

| Step | Cntr | Condition Type | Description | From | To | Subtotal | Reqt | CalType | BasType |
|------|------|----------------|-------------|------|----|----------|------|---------|---------|
| 10   | 1    | PR00           | Giá gốc     |      |    |          |      |         |         |
| 20   | 1    | K004           | Chiết khấu  |      |    |          |      |         |         |
| 30   | 1    |                | **TỔNG SAU CK** | 10   | 20 | 1        |      |         |         |
| 40   | 1    | KF00           | Vận chuyển  |      |    |          |      |         |         |
| 50   | 1    |                | **DOANH THU**   | 30   | 40 | 2        |      |         |         |
| 60   | 1    | MWST           | Thuế GTGT   | 50   |    |          | 10   |         |         |
| 70   | 1    |                | **KHÁCH PHẢI TRẢ**| 50   | 60 | A        |      |         |         |

### Ý nghĩa các cột:
* **Step (Bước):** Thứ tự tính toán từ trên xuống.
* **From - To:** Cực kỳ hay! Nhìn vào Step 30, giá trị của nó = Tổng từ Step 10 cộng đến Step 20. Nhìn Step 60 (Thuế), cột From = 50, nghĩa là Thuế chỉ được tính dựa trên "DOANH THU" chứ không lấy giá gốc PR00.
* **Subtotal:** Đẩy giá trị của dòng này sang các bảng tạm khác (VD: Đẩy vào bảng tính tín dụng Credit limit).
* **Reqt (Requirement - Routine):** Một đoạn code ABAP! Chỗ này cho phép lập trình viên viết logic "chỉ kích hoạt dòng này NẾU thỏa mãn điều kiện A, B, C". 
* **CalType (Alternative Calculation Type):** Gắn code ABAP để thay đổi công thức tính toán mặc định của SAP thành công thức tính toán phức tạp do doanh nghiệp tự nghĩ ra.

---

## 3. Cơ Chế Xác Định (Determination) - `OVKK`
Khi gõ T-Code `VA01` để tạo đơn hàng, SAP làm sao biết lôi cái bảng tính giá nào ra?
Nó dùng quy tắc:
**Sales Area** (Tổ chức bán hàng + Kênh PP + Ngành) + **Customer Pricing Proc.** (Khách hàng lẻ hay sỉ) + **Document Pricing Proc.** (Loại đơn đặt hàng) = **Pricing Procedure được chọn**.
