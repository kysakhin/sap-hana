# Module 3

## Task 1: Create and consume an OData service on GBI_DEMO_PRODUCT
**PDF:** `06_Native_Development_Chapter_02_OData_in_SAP_HANA_Case_Study_en` (curriculum 2), first section

1. Editor: right-click `gbi-student-###` → New → Package → `UI5App`.
2. Right-click `UI5App` → **Create Application** → template *Empty application (with XSAccess and XSApp)* → Create.
3. Right-click `UI5App` → New → Package → `models`.
4. Right-click `models` → New → File → `GBI.xsodata`. Enter `service {}`, then right-click the file → **Activate**.
5. Replace the content and save (Ctrl+S):
```text
service {
 "GBI_DEMO"."GBI_DEMO_PRODUCT" as "Products";
}
```
Own table instead (name goes inside one pair of quotes after the schema):
```text
service {
 "GBI_###"."gbi-student-###.<subpackage>::PRODUCT_ATTR_###" as "Products";
}
```
6. Right-click the file → **Open OData Explorer** to browse the data. Select the file and click the green **Run** button in the toolbar to open the raw service in the browser.

**Test URLs**
```text
http://<host>:<port>/gbi-student-###/UI5App/models/GBI.xsodata/$metadata
http://<host>:<port>/gbi-student-###/UI5App/models/GBI.xsodata/Products
http://<host>:<port>/gbi-student-###/UI5App/models/GBI.xsodata/Products('BOTL1000')
http://<host>:<port>/gbi-student-###/UI5App/models/GBI.xsodata/Products/?$top=5&$skip=10
http://<host>:<port>/gbi-student-###/UI5App/models/GBI.xsodata/Products/?$format=json
```

## Task 2: MVC Hello World app
**PDF:** `10_Native_Development_Chapter_03_SAPUI5_Exercise_#2_en` (curriculum 2)

1. Right-click `gbi-student-###` → New → Package → `MVCHelloWorld`.
2. Right-click `MVCHelloWorld` → **Create Application** → *Empty application (with XSAccess and XSApp)*.
3. Open `index.html`, delete everything, click **Insert Snippet** → *Empty UI5 frame*. Then change:
   - `<title>` to `MVC Hello World`
   - `data-sap-ui-libs` to `sap.m`
   - the second `<script>` tag: empty it
   - the `<div>` id from `content` to `uiArea`
4. Right-click `MVCHelloWorld` → New → Package → `content`.
5. In `content`, create `Main.view.xml`:
```xml
<mvc:View controllerName="content.Main"
    xmlns:mvc="sap.ui.core.mvc"
    xmlns="sap.m">
    <Button id="mybutton" text="Hello World" press="changeText"/>
</mvc:View>
```
6. In `content`, create `Main.controller.js`:
```javascript
sap.ui.controller("content.Main", {
    onInit: function() {},
    onExit: function() {},
    onAfterRendering: function() {},
    onBeforeRendering: function() {},
    changeText : function(oEvent) {
        var myButton = oEvent.getSource();
        myButton.setText("Clicked!");
    }
});
```
7. In `index.html`, put this in the second (empty) `<script>` tag:
```javascript
sap.ui.localResources("content");
content_view = sap.ui.view({
    id:"idmain1",
    viewName:"content.Main",
    type:sap.ui.core.mvc.ViewType.XML
});
content_view.placeAt("uiArea");
```
8. Save all, then click **Run** on `index.html`.

**Test URL**
```text
http://<host>:<port>/gbi-student-###/MVCHelloWorld/index.html
```
Click the button: the text changes to "Clicked!".

## Task 3: Calculation views for CUSTOMER, PRODUCT and SALES
**PDF:** `06_Introduction_to_SAP_HANA_Chapter_5_Case_Study_en` (curriculum 1), Steps 3, 4, 5

Editor: right-click `gbi-student-###` → New → Calculation View.

### Customer: `CUSTOMER_DIM_CV_###` (Data category: **Dimension**)
1. Add a new **Projection** node. Add table `CUSTOMER_ATTR_###` and map all fields to output except `VALID_FROM`.
2. In this projection add a filter. It only appears on a non-default projection node: `"VALID_TO"=99991231`
3. Add a **Join** node (`JOIN_1`), input `PROJECTION_1`, add table `GBI_DEMO_COUNTRY`:
   - join on `COUNTRY`, type **Text Join**, language column `GBI_DEMO_COUNTRY.LANGU`
   - output all `PROJECTION_1` fields, plus `SHORT_TEXT` renamed to `SHORT_TEXT_COUNTRY`
4. Add a second **Join** node (`JOIN_2`), input `JOIN_1`, add table `GBI_DEMO_SALESORG`:
   - join on `SALES_ORGANIZATION`, type **Text Join**, language column `GBI_DEMO_SALESORG.LANGU`
   - output all `JOIN_1` fields except `VALID_TO`, plus `SHORT_TEXT` renamed to `SHORT_TEXT_SALESORG`
5. Link the last join to the top **Projection** node and add all fields as output columns.
6. **Semantics** node:
   - `CUSTOMER_NUMBER`: Key
   - `COUNTRY`: label column `SHORT_TEXT_COUNTRY`
   - `SALES_ORGANIZATION`: label column `SHORT_TEXT_SALESORG`
   - hide `SHORT_TEXT_COUNTRY` and `SHORT_TEXT_SALESORG`
7. Save and activate, then **Data Preview**.

### Product: `PRODUCT_DIM_CV_###` (Data category: **Dimension**)
1. In the existing projection node, add table `PRODUCT_ATTR_###`.
2. Add as output: `PRODUCT` (Key), `PRODUCT_NAME`, `PRODUCT_CATEGORY`, `DIVISION`, `COLOR`, `PRODUCT_GROUP`, `INTERNAL_PRICE`. Do not add `PRICE`.
3. Save and activate, then Data Preview.

### Sales: `SALES_CUBE_CV_###` (Data category: **Cube**, tick *With star join*)
1. Add a projection node with table `SALES_###`. Output all fields except `DAY`. Link it to the star join node.
2. In the projection add a calculated column:
   - Name `NET_REVENUE`, data type Decimal(17,2)
   - Formula: `"REVENUE"-"DISCOUNT"`
3. Star join node:
   - join with `CUSTOMER_DIM_CV_###` on `CUSTOMER_NUMBER`, referential join
   - join with `PRODUCT_DIM_CV_###` on `PRODUCT`, referential join
   - output all fields except `CUSTOMER_NUMBER` and `PRODUCT`
4. Semantics node, set types:

| Field | Type |
|---|---|
| YEAR, MONTH, ORDER_NUMBER, ORDER_ITEM, UNIT_OF_MEASURE, CURRENCY | attribute |
| SALES_QUANTITY, REVENUE, DISCOUNT, NET_REVENUE | measure |

5. Save and activate, then Data Preview.
6. For Lumira only: close the graphical editor, open the view in the text editor, delete `dimensionType="STANDARD"` on line 2, save.

## Task 4: OData service on calculation views
**PDF:** `06_Native_Development_Chapter_02_OData_in_SAP_HANA_Case_Study_en` (curriculum 2), second section

1. Open `GBI.xsodata` in `gbi-student-###/UI5App/models`.
2. Replace the content with this and save. The demo view is shown, and you can use your own view in the same way (`"gbi-student-###.<subpackage>::SALES_CUBE_CV_###"`):
```text
service {
 "GBI_DEMO"."GBI_DEMO_PRODUCT" as "Products";
 "GBI_DEMO"."GBI_DEMO_CUSTOMER" as "Customers";
 "gbi-demo.WebIDE::GBI_DEMO_SALES_CUBE_CV" as "Sales"
    keys generate local "ID"
    aggregates always;
 "gbi-demo.WebIDE::GBI_DEMO_SALES_CUBE_CV" as "SalesOrders"
    with ("MONTH", "YEAR", "CITY", "NET_REVENUE", "REVENUE")
    keys generate local "ID"
    aggregates always (SUM of "NET_REVENUE", SUM of "REVENUE");
}
```
3. Select the file → **Run**, or **Open OData Explorer** and pick the `Sales` endpoint.

**Test URLs**
```text
http://<host>:<port>/gbi-student-###/UI5App/models/GBI.xsodata
http://<host>:<port>/gbi-student-###/UI5App/models/GBI.xsodata/Sales?$select=CUSTOMER_NAME,REVENUE&$orderby=REVENUE&$desc&$top=5
http://<host>:<port>/gbi-student-###/UI5App/models/GBI.xsodata/SalesOrders?$filter=YEAR eq 2007
```
For a dimension view (no `aggregates`), expose it like this and test `/Customer?$top=5&$format=json`:
```text
 "gbi-student-###.<subpackage>::CUSTOMER_CV" as "Customer"
    keys generate local "ID";
```
