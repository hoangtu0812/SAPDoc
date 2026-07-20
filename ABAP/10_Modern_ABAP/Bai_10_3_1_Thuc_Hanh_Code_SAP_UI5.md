# 💻 Bài 10.3.1: Thực Hành Code SAP UI5 (Frontend Fiori)

Để làm một ứng dụng SAP Fiori chuẩn, bạn phải viết Frontend bằng SAP UI5 (Kết hợp giữa XML cho giao diện và JavaScript cho Logic).

Dưới đây là một bộ code hoàn chỉnh của một ứng dụng UI5 cơ bản: **Hiển thị một bảng danh sách Vật tư lấy từ OData Service.**

---

### 1. View (Giao diện) - File: `App.view.xml`
Giao diện trong UI5 được vẽ bằng thẻ XML (Rất giống HTML nhưng dùng các thẻ chuẩn của thư viện `sap.m`).

```xml
<mvc:View
    controllerName="my.company.app.controller.App"
    xmlns:mvc="sap.ui.core.mvc"
    xmlns="sap.m">
    
    <!-- Trang chính của ứng dụng -->
    <Page title="Danh Sách Vật Tư">
        <content>
            <!-- Tạo một cái Bảng (Table). 
                 items="{/MaterialSet}" nghĩa là trỏ trực tiếp (Bind) tới EntitySet của OData -->
            <Table id="idMaterialTable" items="{/MaterialSet}">
                <!-- Cấu hình các Cột -->
                <columns>
                    <Column><Text text="Mã Vật Tư" /></Column>
                    <Column><Text text="Loại Vật Tư" /></Column>
                    <Column><Text text="Ngày Tạo" /></Column>
                </columns>
                <!-- Cấu hình các Dòng (Bind các thuộc tính JSON vào) -->
                <items>
                    <ColumnListItem>
                        <cells>
                            <Text text="{Matnr}" />
                            <Text text="{Mtart}" />
                            <!-- Format kiểu ngày tháng -->
                            <Text text="{ path: 'Ersda', 
                                          type: 'sap.ui.model.type.Date', 
                                          formatOptions: { pattern: 'dd/MM/yyyy' } }" />
                        </cells>
                    </ColumnListItem>
                </items>
            </Table>
            
            <Button text="Tạo Vật Tư Mới" press="onCreatePress" type="Emphasized" class="sapUiTinyMargin"/>
        </content>
    </Page>
</mvc:View>
```

---

### 2. Controller (Logic) - File: `App.controller.js`
Nơi chứa code JavaScript để hứng các sự kiện (Ví dụ: Khi bấm nút "Tạo Vật Tư Mới").

```javascript
sap.ui.define([
    "sap/ui/core/mvc/Controller",
    "sap/m/MessageToast" // Thư viện hiển thị thông báo góc dưới
], function (Controller, MessageToast) {
    "use strict";

    return Controller.extend("my.company.app.controller.App", {
        
        // Hàm này chạy đầu tiên khi ứng dụng được bật lên (Giống START-OF-SELECTION của ABAP)
        onInit: function () {
            // Lấy OData Model đã được khai báo sẵn trong manifest.json
            var oModel = this.getOwnerComponent().getModel();
            this.getView().setModel(oModel);
        },

        // Sự kiện khi bấm nút tạo
        onCreatePress: function () {
            var oModel = this.getView().getModel();
            
            // Khai báo cục dữ liệu JSON muốn POST lên Backend
            var oPayload = {
                Matnr: "NEW_MAT_01",
                Mtart: "FERT"
            };

            // Gọi hàm Create của OData (Bắn API POST lên Backend SEGW)
            oModel.create("/MaterialSet", oPayload, {
                success: function (oData, response) {
                    // Nếu Backend trả về 200 OK
                    MessageToast.show("Tạo vật tư thành công!");
                },
                error: function (oError) {
                    // Nếu Backend trả về lỗi (Ví dụ: Exception đã ném ở bài trước)
                    MessageToast.show("Lỗi tạo vật tư. Vui lòng kiểm tra lại.");
                }
            });
        }
    });
});
```

### 🔍 Giải thích cấu trúc Ứng dụng:
1. `manifest.json` (Không show ở đây): Là file cấu hình trung tâm. Trong đó bạn khai báo đường link của OData Service (Ví dụ `/sap/opu/odata/sap/Z_MATERIAL_SRV/`). UI5 sẽ tự động lấy link đó để giao tiếp với backend.
2. `Binding dữ liệu {/MaterialSet}`: Phép thuật của UI5 nằm ở đây. Bạn **không cần** viết vòng lặp `FOR` bằng Javascript để vẽ bảng. Chỉ cần nhét `{/MaterialSet}` vào thẻ `<Table>`, thư viện UI5 sẽ tự gửi lệnh GET xuống SAP Backend, lấy 100 dòng JSON về và tự động sinh ra 100 thẻ `<ColumnListItem>` trên màn hình!
