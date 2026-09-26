Primary source: UN Comtrade
Purpose: Defininf the first reproducible dataset before database, API, and dashboard development. 

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
**Note**: This helps us understand the dependence of one country on the supplier. 
3. Exclude aggregate partners such as World, regions, free zones, bunkers, and unspecified paterns from supplier-share and HHI calculations. Preserve them in raw data for auditing. 
**Note**: Including partners such as World as another supplier would make the calculation meaningless. But we need to preserve this raw data because it will be useful for checking reported totals, finding unexplained trade, auditing extraction, and investigating differences between total and bilateral records
