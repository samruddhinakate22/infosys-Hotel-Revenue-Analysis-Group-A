# Module 2: Occupancy & Revenue Metrics
## Overview
This module focuses on analyzing hotel performance through core occupancy and revenue indicators. It provides a structured view of how room utilization and pricing translate into revenue over time, enabling stakeholders to monitor trends, compare booking channels, and evaluate seasonal patterns.

## Objective
The objective of this module is to track and evaluate Occupancy Rate, Average Daily Rate (ADR), and Revenue Per Available Room (RevPAR) across different time granularities and booking sources. The insights from this module support demand analysis, pricing strategy, and channel performance evaluation.

## Key Focus Areas
- Measurement of occupancy and revenue efficiency
- Time-based performance analysis (daily, weekly, seasonal)
- Comparison of Direct vs OTA booking contributions
- High-level KPI monitoring with interactive drilldowns

## Procedure:
### Step - 1: 
Removed Company Column because 98% of values are NULL only.

### Step - 2:
Created Revenue Column
```
Revenue = if [is_canceled] = 1 then 0 else [adr]*[total_nights]
```

Created Room_Category Column
```
room_category = Table.AddColumn(#"Changed Type2", "room_category", each if List.Contains({"A","B"}, [reserved_room_type]) then "Standard"
else if List.Contains({"C","D"}, [reserved_room_type]) then "Deluxe"
else if List.Contains({"E","F","G"}, [reserved_room_type]) then "Suite"
else "Economy")
```

### Step - 3: New Measures
#### Total Revenue
``` DAX
Total Revenue =
SUM ( 'Raw_Fact'[Revenue] )
```

#### Total Bookings
``` DAX
Total Bookings = 
CALCULATE (
    COUNT ( 'Raw_Fact'[booking_id] ),
    'Raw_Fact'[is_canceled] = 0
)
```

#### ADR (Average Daily Rate)
Why we are creating again ADR even though adr os prresent in raw table  because:
- Column ADR ≠ KPI ADR
- Column adr = rate for one booking / one stay
- KPI ADR = average rate across many bookings, dates, hotels, channels
- column adr we get
- Each booking weighted equally
- 1-night stay = 7-night stay ❌
- That’s statistically incorrect.
- ADR measure :
- Gives longer stays proper weight
- Matches hotel industry definition
- Produces correct values at any aggregation level

``` DAX
ADR(Avg Daily Rate) = 
DIVIDE (
    [Total Revenue],
    CALCULATE (
        SUM ( 'Raw_Fact'[total_nights] ),
        'Raw_Fact'[is_canceled] = 0
    )
)
```

#### Occupancy%
- Occupancy % = Occupied Room Nights ÷ Available Room Nights
- What we use from our data
     - total_nights → occupied room nights per booking
     - is_canceled → to exclude canceled stays
     - roomid / hotel_id → rooms context (handled via model)

##### Step 1: Measure — Occupied Room Nights
``` DAX
Occupied Room Nights =
CALCULATE (
    SUM ( 'Raw_Fact'[total_nights] ),
    'Raw_Fact'[is_canceled] = 0
)
```

##### Step 2: Measure — Available Room Nights
This depends on your room × date model.
``` DAX
Available Room Nights = 
COUNTROWS ( 'room-dimension' )
* COUNTROWS ( 'date-dimension' )
```
##### Step 3: Occupancy % measure
``` DAX
Occupancy % =
DIVIDE (
    [Occupied Room Nights],
    [Available Room Nights]
)
```

#### Measure: RevPAR (Revenue Per Available Room)
RevPAR = Total Revenue ÷ Available Room Nights
``` DAX
RevPAR =
DIVIDE (
    [Total Revenue],
    [Available Room Nights]
)
```

### Step 4: Report Analysis


## Outcome
By the end of this module, users will be able to clearly understand how occupancy and pricing impact overall revenue, identify peak and low-demand periods, and assess the effectiveness of different booking channels through intuitive dashboards and KPIs.
