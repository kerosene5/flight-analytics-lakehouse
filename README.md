# Flight Analytics Data Lakehouse

![Records](https://img.shields.io/badge/records-30M%2B-brightgreen)
![Status](https://img.shields.io/badge/status-in%20progress-orange)

End-to-end data engineering and analytics project for building a cloud-based flight performance lakehouse on Microsoft Azure. The project uses the Kaggle **Flight Status Prediction** dataset and follows a medallion architecture from raw ingestion to curated analytical outputs for Tableau dashboards.

The goal is to learn and demonstrate both sides of the workflow: reliable data engineering in Azure and practical data analysis through business KPIs.

## Scope

This repository documents a complete flight analytics pipeline:

1. Ingest raw flight files into Azure Data Lake Storage Gen2.
2. Store source data in a Bronze layer without changing its original structure.
3. Use Azure Synapse Analytics to clean, standardize, and transform Bronze data into Silver.
4. Build Gold-layer fact and dimension tables for reporting.
5. Use the Gold layer as the analytics-ready source for Tableau dashboards.

```mermaid
flowchart LR
    A["Kaggle Flight Status Prediction Dataset"] --> B["ADLS Gen2 Bronze Container"]
    B --> C["Azure Synapse Analytics"]
    C --> D["ADLS Gen2 Silver Container"]
    D --> E["Gold Layer: Fact and Dimension Tables"]
    E --> F["Tableau Dashboard"]
```

## Dataset

The dataset is sourced from Kaggle: [Flight Status Prediction](https://www.kaggle.com/datasets/robikscube/flight-delay-dataset-20182022). It contains U.S. flight records from 2018 to 2022, including flight dates, airlines, origin and destination airports, scheduled and actual departure times, delay values, cancellations, diversions, route distance, and delay reason fields.

The source dataset is based on the U.S. Department of Transportation On-Time Performance data. For this project, the working files are yearly Parquet files plus an airline lookup table.

| File | Purpose |
| --- | --- |
| `Combined_Flights_2018.parquet` to `Combined_Flights_2022.parquet` | Yearly flight status records used as the main fact source |
| `Airlines.csv` | Airline code and description lookup |

## Current Azure Setup

The cloud environment is built in Microsoft Azure:

| Component | Current Use |
| --- | --- |
| Azure Data Lake Storage Gen2 | Main storage account for the lakehouse |
| Bronze container | Raw source files loaded from Kaggle |
| Silver container | Cleaned and standardized flight data |
| Gold container | Planned curated fact and dimension model for reporting |
| Azure Synapse Analytics | Cloud transformation layer from Bronze to Silver, then Silver to Gold |
| Tableau | Final dashboard and KPI visualization layer |

The Bronze container currently contains the airline lookup file and yearly flight Parquet files for 2018-2022.

## Medallion Architecture

### Bronze

The Bronze layer stores raw source files exactly as received. This layer is used for reproducibility and auditability. No business logic is applied here.

Expected responsibilities:

- Preserve original Kaggle files.
- Store yearly flight Parquet files and lookup data.
- Keep raw data separate from cleaned and modeled data.
- Provide a stable input source for Synapse transformations.

### Silver

The Silver layer contains cleaned, typed, and analysis-ready flight records. This is the current development focus.

Expected transformations:

- Read Bronze Parquet and CSV files from ADLS Gen2.
- Standardize column names and data types.
- Handle nulls, invalid values, and inconsistent time fields.
- Join airline descriptions where needed.
- Keep useful operational fields for delay, cancellation, diversion, airport, airline, route, and time analysis.
- Write cleaned output back to the Silver container.

### Gold

The Gold layer will contain curated tables designed for analytics and dashboard performance. This layer will be modeled around facts and dimensions rather than raw files.

Planned Gold model:

| Table | Purpose |
| --- | --- |
| `fact_flights` | Flight-level metrics such as delay minutes, cancellation flag, diversion flag, distance, and flight count |
| `dim_airline` | Airline code, airline name, and airline-level attributes |
| `dim_airport` | Origin and destination airport details |
| `dim_route` | Origin-destination route combinations |
| `dim_date` | Calendar attributes such as day, month, quarter, year, weekday, and season |

## Working Data Sheet

The project focuses on a practical subset of the full source schema for KPI development.

### Combined Flights

| Column | Description | Type |
| --- | --- | --- |
| `FlightDate` | Flight date | `DATE` |
| `Airline` | Airline carrier | `VARCHAR` |
| `Origin` | Origin airport | `VARCHAR` |
| `Dest` | Destination airport | `VARCHAR` |
| `Cancelled` | Whether the flight was cancelled | `BOOLEAN` |
| `Diverted` | Whether the flight was diverted | `BOOLEAN` |
| `CRSDepTime` | Scheduled departure time | `INTEGER` |
| `DepTime` | Actual departure time | `DECIMAL` |
| `DepDelayMinutes` | Departure delay in minutes, with early departures set to 0 | `DECIMAL` |
| `DepDelay` | Departure delay offset, including early departures | `INTEGER` |

### Airlines

| Column | Description | Type |
| --- | --- | --- |
| `Code` | Airline code | `VARCHAR` |
| `Description` | Airline description | `VARCHAR` |

## Business Questions

The Gold layer and Tableau dashboard are designed to answer the following questions:

- Which airports have the most flight delays?
- Which airlines have the highest and lowest on-time performance?
- What seasonal trends exist in delays and cancellations?
- Which routes are the least reliable?
- What factors have the strongest effect on flight delays?
- Which airlines perform best under high-traffic conditions?

## KPI Plan

| KPI Area | Metrics |
| --- | --- |
| Overall performance | Total flights, on-time performance, average departure delay |
| Disruption tracking | Cancellation rate, diversion rate |
| Airline analysis | Best and worst airlines, delay segmentation, reliability score |
| Airport analysis | Most delayed airports, most reliable airports, congestion index |
| Route analysis | Worst routes, route reliability |
| Time analysis | Delay by day, month, and season |

Example KPI definitions:

- **On-Time Performance (OTP):** flights delayed less than 15 minutes divided by total flights.
- **Average Departure Delay:** average of `DepDelayMinutes`.
- **Cancellation Rate:** cancelled flights divided by total flights.
- **Diversion Rate:** diverted flights divided by total flights.
- **Airport Congestion Index:** flights per day multiplied by average delay.

## Repository Structure

The main README gives the project overview. Separate layer-specific documentation will describe implementation details.

```text
.
+-- README.md
+-- Bronze.md
+-- Silver.md
+-- Gold.md
+-- synapse/
|   +-- bronze_to_silver/
|   +-- silver_to_gold/
+-- sql/
+-- tableau/
+-- docs/
```

## Project Status

| Stage | Status |
| --- | --- |
| Dataset selected | Complete |
| Azure storage account created | Complete |
| Bronze, Silver, and Gold containers created | Complete |
| Bronze files uploaded | Complete |
| Bronze to Silver transformation in Synapse | In progress |
| Gold fact and dimension model | Planned |
| Tableau dashboard | Planned |

## Outcome

The final output will be a cloud-based data lakehouse that transforms raw flight status records into clean, modeled, dashboard-ready data. The Tableau dashboard will focus on operational flight performance, delay behavior, airline reliability, airport congestion, and route-level trends.


