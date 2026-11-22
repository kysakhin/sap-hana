# Module 4

## Tasks 1 and 2: Table function for customer segmentation, and a calc view for a given year
No source PDF or notes were provided for these two in the mapping, so there are no steps here yet.

## Task 3: Forecast next year's revenue with triple exponential smoothing
**Source:** `25NHOP715-SAP-forecasting-Module-4.docx` (Task 15). Related PDF: `06_Data_Mining_Chapter_01_Forecasting_Case_Study_en` (curriculum 3), which does the same in the Predictive Analysis desktop tool.

Catalog perspective → select schema `GBI_###` → **SQL Console**. Run each statement in turn.

**1. Inspect the procedure (optional)**
```sql
SELECT SCHEMA_NAME, PROCEDURE_NAME, PARAMETER_NAME, POSITION, DATA_TYPE_NAME,
       PARAMETER_TYPE, TABLE_TYPE_SCHEMA, TABLE_TYPE_NAME
FROM SYS.PROCEDURE_PARAMETERS
WHERE SCHEMA_NAME = '_SYS_AFL' AND PROCEDURE_NAME = 'GBI_211_PAS3_1_TRIPLESMOOTH'
ORDER BY POSITION;

SELECT PARAMETER_NAME, PARAMETER_POSITION, COLUMN_NAME, POSITION AS COLUMN_POSITION,
       DATA_TYPE_NAME, LENGTH, SCALE
FROM SYS.PROCEDURE_PARAMETER_COLUMNS
WHERE SCHEMA_NAME = '_SYS_AFL' AND PROCEDURE_NAME = 'GBI_211_PAS3_1_TRIPLESMOOTH'
ORDER BY PARAMETER_POSITION, POSITION;
```
P1 = input data, P2 = parameters, P3 = output.

**2. Input table**
```sql
CREATE COLUMN TABLE "GBI_###"."SALES_REVENUE_TS_PAL" (
    "__ID__" INTEGER,
    "REVENUE_EUR" DOUBLE
);
```

**3. Fill it, then verify**
```sql
INSERT INTO "GBI_###"."SALES_REVENUE_TS_PAL" ("__ID__", "REVENUE_EUR")
SELECT
    ROW_NUMBER() OVER (ORDER BY "YEAR", "MONTH") AS "ID",
    SUM("REVENUE_EUR") AS "REVENUE_EUR"
FROM "_SYS_BIC"."gbi-demo/GBI_DEMO_REVENUE_FORECAST_AV"
GROUP BY "YEAR", "MONTH";

SELECT * FROM "GBI_###"."SALES_REVENUE_TS_PAL";
```

**4. Parameter table**
```sql
CREATE COLUMN TABLE "GBI_###"."TS_PARAM" (
    "Name"       VARCHAR(50),
    "intArgs"    INTEGER,
    "doubleArgs" DOUBLE,
    "strArgs"    VARCHAR(100)
);

INSERT INTO "GBI_###"."TS_PARAM" VALUES ('ALPHA', NULL, 0.1, NULL);
INSERT INTO "GBI_###"."TS_PARAM" VALUES ('BETA', NULL, 0.1, NULL);
INSERT INTO "GBI_###"."TS_PARAM" VALUES ('GAMMA', NULL, 0.1, NULL);
INSERT INTO "GBI_###"."TS_PARAM" VALUES ('CYCLE', 12, NULL, NULL);
INSERT INTO "GBI_###"."TS_PARAM" VALUES ('FORECAST_NUM', 12, NULL, NULL);
INSERT INTO "GBI_###"."TS_PARAM" VALUES ('SEASONAL', 1, NULL, NULL);
INSERT INTO "GBI_###"."TS_PARAM" VALUES ('INITIAL_METHOD', 1, NULL, NULL);

SELECT * FROM "GBI_###"."TS_PARAM";
```

**5. Run the forecast**
```sql
CALL "_SYS_AFL"."GBI_211_PAS3_1_TRIPLESMOOTH"(
    "GBI_###"."SALES_REVENUE_TS_PAL",
    "GBI_###"."TS_PARAM",
    ?
);
```
Result: 12 rows with `TIME` and `Forecasted_Revenue`, one per month of next year.
