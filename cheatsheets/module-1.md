# Module 1

## Task 1: Web-based Development Workbench basics
**PDF:** `03_Native_Development_Chapter_01_Web-based_Develeopment_Workbench_Exercise_en` (curriculum 2)

1. Open the Fiori Launchpad: `http://<host>:<port>/` and log in.
2. Curriculum #2 - Native Development → **SAP HANA Editor** tile.
3. Right-click package `gbi-demo` to see the context menu (new package, new file, refresh, delete).
4. Expand `gbi-demo` → `UI5App`. Click `index.html` once. The editor highlights it as HTML and the toolbar gets a **Run** button.
5. Click **Run**. It opens `index.html` in a new browser window. Close it.
6. Click `.xsaccess` too. Both files now show as tabs.
7. Right-click the `index.html` tab → **Close Others**.
8. Double-click a tab to collapse or restore the left panel.

## Task 2: Display a table in the Catalog perspective
**PDF:** `03_Introduction_to_SAP_HANA_Chapter_3_Data_Modeling_Exercise_en` (curriculum 1), Task 2

1. Web Workbench: `http://<host>:<port>/sap/hana/ide/` → **Catalog**.
2. Catalog → `GBI_DEMO` → Tables → `GBI_DEMO_CUSTOMER`.
3. Right-click the table → **Open Definition** (check COUNTRY data type and length, and Column or Row store).
4. Right-click the table → **Open Content** to see the rows.

## Task 3: Display a calculation view in the Editor perspective
**PDF:** `03_Introduction_to_SAP_HANA_Chapter_3_Data_Modeling_Exercise_en` (curriculum 1), Task 3

1. Welcome screen → **Editor**.
2. Content → `gbi-demo` → `WebIDE` → double-click `GBI_DEMO_PROD_DIM_CV.calculationview`.
3. Click the **Projection** node.
4. The **Mapping** section shows the schema and table used and the column mappings.

## Task 4: Create the Customer, Product and Sales tables and load data
**PDF:** `06_Introduction_to_SAP_HANA_Chapter_5_Case_Study_en` (curriculum 1), Step 1 (tables) and Step 2 (loading, see Module 5)

Editor perspective. Right-click package `gbi-student-###` → New → table definition (`.hdbtable`). After each one, check it in Catalog → `GBI_###` → Tables.

**CUSTOMER_ATTR_###.hdbtable**
```text
table.schemaName = "GBI_###";
table.tableType = COLUMNSTORE;
table.columns = [
    {name = "CUSTOMER_NUMBER"; sqlType = NVARCHAR; length = 10; },
    {name = "CUSTOMER_NAME"; sqlType = NVARCHAR; length = 20; },
    {name = "CITY"; sqlType = NVARCHAR; length = 35; },
    {name = "VALID_TO"; sqlType = DATE; },
    {name = "VALID_FROM"; sqlType = DATE; },
    {name = "SALES_ORGANIZATION"; sqlType = NVARCHAR; length = 4; },
    {name = "COUNTRY"; sqlType = NVARCHAR; length = 2; }
];
table.primaryKey.pkcolumns = ["CUSTOMER_NUMBER", "VALID_TO"];
```

**PRODUCT_ATTR_###.hdbtable** (key: PRODUCT)
```text
table.schemaName = "GBI_###";
table.tableType = COLUMNSTORE;
table.columns = [
    {name = "PRODUCT"; sqlType = NVARCHAR; length = 8; },
    {name = "PRODUCT_NAME"; sqlType = NVARCHAR; length = 40; },
    {name = "PRODUCT_CATEGORY"; sqlType = NVARCHAR; length = 3; },
    {name = "DIVISION"; sqlType = NVARCHAR; length = 2; },
    {name = "INTERNAL_PRICE"; sqlType = DECIMAL; precision = 17; scale = 2; },
    {name = "PRICE"; sqlType = DECIMAL; precision = 17; scale = 2; },
    {name = "COLOR"; sqlType = NVARCHAR; length = 10; },
    {name = "PRODUCT_GROUP"; sqlType = NVARCHAR; length = 20; }
];
table.primaryKey.pkcolumns = ["PRODUCT"];
```

**SALES_###.hdbtable** (keys: ORDER_NUMBER, ORDER_ITEM)
```text
table.schemaName = "GBI_###";
table.tableType = COLUMNSTORE;
table.columns = [
    {name = "YEAR"; sqlType = INTEGER; },
    {name = "MONTH"; sqlType = INTEGER; },
    {name = "DAY"; sqlType = INTEGER; },
    {name = "CUSTOMER_NUMBER"; sqlType = NVARCHAR; length = 10; },
    {name = "ORDER_NUMBER"; sqlType = NVARCHAR; length = 10; },
    {name = "ORDER_ITEM"; sqlType = NVARCHAR; length = 3; },
    {name = "PRODUCT"; sqlType = NVARCHAR; length = 8; },
    {name = "SALES_QUANTITY"; sqlType = INTEGER; },
    {name = "UNIT_OF_MEASURE"; sqlType = NVARCHAR; length = 3; },
    {name = "REVENUE"; sqlType = DECIMAL; precision = 17; scale = 2; },
    {name = "CURRENCY"; sqlType = NVARCHAR; length = 3; },
    {name = "DISCOUNT"; sqlType = DECIMAL; precision = 17; scale = 2; }
];
table.primaryKey.pkcolumns = ["ORDER_NUMBER", "ORDER_ITEM"];
```

Then load the data: see **Module 5, Tasks 1 and 2**. Don't re-activate a table file after loading it.
