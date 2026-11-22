# Module 5

## Task 1: Remote source and virtual tables (Smart Data Access)
**PDF:** `06_Introduction_to_SAP_HANA_Chapter_5_Case_Study_en` (curriculum 1), Step 2, points 1 to 3

1. Web Workbench → **Catalog** perspective.
2. In the navigator, Provisioning → Remote Sources → add a new remote source:

| Field | Value |
|---|---|
| Source name | `RemoteSource_###` |
| Adapter Name | `FileAdapter` |
| Location | `agent` |
| Agent (Group) name | `dpagent` |
| Root directory | `/GBI_dataset` |
| Directory fileformat def. | `/GBI_dataset` |
| Credentials mode | Technical User |
| Access token | `gbi` |

3. **Save**, then refresh the remote source to see `Customer.csv`, `Product.csv` and `Sales.csv`.
4. Right-click each file → create virtual table:

| File | Table name | Schema |
|---|---|---|
| Customer.csv | `VT_Customer_###.csv` | `GBI_###` |
| Product.csv | `VT_Product_###.csv` | `GBI_###` |
| Sales.csv | `VT_Salesdata_###.csv` | `GBI_###` |

## Task 2: Flowgraphs to populate CUSTOMER, PRODUCT and SALES
**PDF:** `06_Introduction_to_SAP_HANA_Chapter_5_Case_Study_en` (curriculum 1), Step 2, points 4 to 8

Do this once per table:

| Flowgraph | Source | Target |
|---|---|---|
| `CustomerFlow` | `VT_Customer_###.csv` | `CUSTOMER_ATTR_###` |
| `ProductFlow` | `VT_Product_###.csv` | `PRODUCT_ATTR_###` |
| `SalesFlow` | `VT_Salesdata_###.csv` | `SALES_###` |

1. Editor: right-click `gbi-student-###` → New → Flow Graph → name it (e.g. `CustomerFlow`).
2. Flowgraph **properties** (top left) → target schema `GBI_###`.
3. Drag a **Data Source** node in → pick the virtual table.
4. Drag a **Data Sink** node in → pick the target table. Connect source → sink.
5. Double-click the Data Sink and map the fields (drag the circle from the source field onto the target field).

Customer mapping:

| Input (file) | Output (table) |
|---|---|
| CustomerNumber | CUSTOMER_NUMBER |
| CustomerName | CUSTOMER_NAME |
| City | CITY |
| ValidTo | VALID_TO |
| ValidFrom | VALID_FROM |
| SalesOrganisation | SALES_ORGANIZATION |
| Country | COUNTRY |

6. **Back**, then Save (Ctrl+S).
7. Right-click the flowgraph in the navigator → **Execute**.
8. Check the status in the monitoring console, then Catalog → `GBI_###` → Tables → right-click the table → **Open Content**.

## Task 3: ETL replication task for customer latitude and longitude (geocoding)
**PDF:** `08_Introduction_to_SAP_HANA_Chapter_5_Geocoding_Exercise_(optional)_en` (curriculum 1)

1. Catalog → **SQL Console**:
```sql
CREATE COLUMN TABLE "GBI_###"."CUSTOMER_GPS_###" (
    "CustomerNumber" NVARCHAR(10),
    "ADDR_LATITUDE_LONGITUDE" ST_POINT(4326) CS_POINT
);
```
2. Editor: right-click `gbi-student-###` → New → Flow Graph → `customer_geocoding_###`. Properties → target schema `GBI_###`.
3. Drag a **Data Source** → select the virtual table `VT_Customer_###.csv`.
4. Drag a **Filter** node, connect the source to it. Double-click → *Filter Node* tab → *Load Elements & Functions*. Expression (drops expired rows):
```text
DAYS_BETWEEN(CURRENT_UTCDATE(), "Filter1_Input"."ValidTo") > 0
```
   Click **Back**.
5. Data Provisioning section → drag a **Geocode** node and connect Filter → Geocode. The address mapping is automatic → **Finish**.
6. Double-click the Geocode node, drag `CustomerNumber` from the left onto the root element `Geocode1` on the right (adds it to the output). **Back**.
7. Drag a **Data Sink** (General) → select `CUSTOMER_GPS_###`. Connect Geocode → sink, double-click the sink and check the mapping.
8. **Back**, Save (it activates), then click **Run** (the execution shows in the console log; the task monitor shows progress).
9. Catalog → `GBI_###` → Tables → `CUSTOMER_GPS_###` → **Open Content**. You should see customer numbers with latitude and longitude.
