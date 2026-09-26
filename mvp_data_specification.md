Primary source: UN Comtrade
Purpose: Defining the first reproducible dataset before database, API, and dashboard development. 

## 1. Research Question:
For each target South Asian country, which countries supplied its urea imports, in what quantity and at what declared trade value from 2018 to 2024. 
The main intention of this dataset is to support later calculation of import tools, supplier shares, supplier concentration (HHI), unit values, and exposure to selected exporting countries. 

## 2. Fixed extraction scope
Frequency of the data: Annual
Years: 2018-2024 inclusive
Importers: Nepal(NPL), Bangladesh(BGD), India(IND), Pakistan(PAK), SriLanka(LKA)
Trade flow: Imports only 
Product classification: Harmonized System(HS) 
>**Note:** The Harmonized System(HS) is an international method for classifying traded products. Each product is assigned a standarized code so countries can report and compare imports and exports consistently. 
In this dataset, '310210' identifies urea. 

Product code: 310210
Product: Urea, whether or not in aqueous solution
Partners/exporters: All individual partner countries reported by each exporter
Transport mode: All modes
Customs procedure: All procedures
Trade system:Use the reporter's published value. 

## 3. Reporting perspective and counting rules
1. Use importer-reported imports as the primary observation (not mixing it with exporter-reported mirror data)
>**Note:** In an ideal scenario importer-reported imports should be equal to exporter-reported mirror data, but practically that is not always the case. The importer's record is generally the better primary perspective because it represents what entered the country's customs system.
But we can use exporter-reported exports if the importer-exported data is missing. 
2. Keep bilateral records for individual partner countries only in the analytical table.
>**Note**: This helps us understand the dependence of one country on the supplier. 
3. Exclude aggregate partners such as World, regions, free zones, bunkers, and unspecified paterns from supplier-share and HHI calculations. Preserve them in raw data for auditing. 
>**Note:** Including partners such as World as another supplier would make the calculation meaningless. But we need to preserve this raw data because it will be useful for checking reported totals, finding unexplained trade, auditing extraction, and investigating differences between total and bilateral records.
4. Do not add bilateral rows to a seperately reported World row; that would double-count trade. 
5. Retaun zero values as reported. Treat missing values as unknown, not zero. 
6. Store source value without manual correction. Any later imputation or mirror data substitution must be a seperate, flagged dataset.

## 4. Output grain
<!-- Exactly what one row in your resulting dataset represents. It is also called level of detail or unit of observation -->
>**Note:** one year * one importing reporter * one exporting partner * HS 310210 * import flow
If the source returns multiple rows at this graim because of the transport mode (it may return seperate rows for seperate means of transportation), custom procedure, second partner, or other breakdowns, aggregate them only after confirming that the rows are multually exclusive. Otherwise request the source's aggregate/ default dimensions. 

## 5. Required Processing Fields

| Field | Type | Meaning / rule |
|---|---|---|
| `year` | integer | Calendar year, 2018–2024 |
| `reporter_code` | string | Source numeric reporter code, retained for traceability |
| `reporter_iso3` | string | Importer ISO 3166-1 alpha-3 code |
| `reporter_name` | string | Importer name from source |
| `partner_code` | string | Source numeric partner code |
| `partner_iso3` | string, nullable | Exporter ISO3; null for source aggregates/unknown partners |
| `partner_name` | string | Exporter or aggregate partner name from source |
| `is_aggregate_partner` | boolean | True for World, regions, unspecified, and other non-country partners |
| `flow_code` | string | Source trade-flow code |
| `flow_name` | string | Must equal imports for analytical rows |
| `classification_code` | string | HS edition/classification returned by source |
| `product_code` | string | Six-character string `310210`; never store as an integer |
| `product_name` | string | Source product description |
| `net_weight_kg` | decimal, nullable | Reported net weight in kilograms |
| `supplementary_quantity` | decimal, nullable | Reported quantity when provided |
| `supplementary_unit` | string, nullable | Unit paired with supplementary quantity |
| `trade_value_usd` | decimal | Current US-dollar trade value reported by source |
| `is_reported` | boolean, nullable | Source reported/estimated flag when available |
| `is_aggregate` | boolean, nullable | Source aggregation flag when available |
| `source` | string | Constant `UN Comtrade` |
| `source_record_id` | string, nullable | Stable record identifier if supplied |
| `retrieved_at_utc` | timestamp | UTC extraction timestamp |
| `raw_file` | string | Relative path to the immutable raw response |

>**note:** Derived measures such as unit_value_usd_per_kg, supplier share, and HHI do not belong in this first cleaned table. They will be calculated downstream so their definitions remain explicit and reproducible. 

## 6. Quantity rule: 
Use net_weight_kg as the comparable physical quantity. Do not automatically replace a missing net weight with the supplementary quantity because its unit may not be kilograms. A later transformation may use supplementary quantity when its unit is explicitly kilograms, and must flag the substitution. 

## 7. Raw-data and processed-data layout

data/
    raw/ 
        un_comtrade/ # Immutable API responses plus request metadata
    processed/
        trade/      # Standarized bilateral annual records

Suggested raw filename: 
un_comtrade_annual_imports_310210_<REPORTER>_<YEAR>_<UTC_TIMESTAMP>.json

Every raw response must be accompanied by enough request metadata to reproduce it: endpoint, query parameters, retrieval time, reponse status, and any source release/ update timestamp. API keys must never be saved in files or logs. 

## 8. Minumum Validation checks
The first extraction is accepted only if it passes these checks:
- Every analytical row has a year, reporter, partner, flow, product code, and trade value. 
- Years fall within 2018-2024 and reporters are one of the five target countries
- Product code is exactly the string 310210, and flow is imports. 
- Trade values and quantities are non-negative when present.
- No duplicate rows exist at the defined ourput grain. 
- All individual-country map to an ISO3 code; mapping failures are reported
- Aggregate partners are clearly flagged and excluded from bilateral metrics. 
- For each reporter-year, the sum of valid bileral partner trade values is compared with --but not forced to equal-- the source's World total; the difference is logged.
- Missing net wright, missing trade value, and source-estimated records are counted in a validation summary.

## 9. Pilot test and success criteria
Before downloading data for every country and year, test the process using India's 2022 Urea imports(HS 310210).
>**Note:** India is just an example. We can use any other countries

The pilot is successful when: 
1. The original UN comtrade response is saved without changes.
2. The data is converted into the columns and formats defined in this specification. 
3. Validation checks identify duplicates, missing values, invalid codes and other problems. 
4. The sum of imports from individual suppliers is compared with that country's reported World Import total, and any difference is recorded.
5. Processing the same raw file again produces the same result. 

After the pilot passes, the extraction can be expanded to all five countries for 2018-2024. 

## 10. Explicitly out of scope for this dataset

- Domestic fertilizer production or consumption
- Phosphate, DAP, potash, or other nitrogen products
- Monthly trade or shipment-level data
- Maritime routes and choketimes
- Natural-gas, fertilizer-price, crop, yield, subsidy, and food-price data
- Causal claims, forecasts, and scenario results
- Mirror-data replacement or imputation


These are later datasets that will join to the stable bileral-trade foundation defined here. 

## 11. Provenance rederences
- UN Statistics Division classification entry for HS 310210:
https://unstats.un.org/unsd/classifications/Econ/Detail/EN/2089/310210 
- UN Comtrade data interface: 
 https://comtradeplus.un.org/ 