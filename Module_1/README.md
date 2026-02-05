# Module 1: Data Preprocessing & Star Schema Design

## Overview

This module focuses on transforming raw hotel booking data into a clean, structured, and analysis-ready format. Using Power Query Editor, the data is enriched with derived columns and organized into a **star schema** model, which forms the foundation for all subsequent analytical modules.

---

## Objective

The objective of this module is to:

* Clean and standardize raw booking data
* Create meaningful derived attributes required for analysis
* Build fact and dimension tables
* Design a scalable **star schema** data model for accurate reporting and performance optimization

---

## Key Focus Areas

* Data integration using append operations
* Feature engineering through calculated columns
* Creation of surrogate keys
* Dimensional modeling using star schema principles
* Preparing data for efficient DAX calculations

---

## Procedure

### Step 1: Create Raw_fact Table

Loaded Table is considered as *Fact Table*

---

### Step 2: Create arrival_date Column

1. Select the following columns:

   * `arrival_date_year`
   * `arrival_date_month`
   * `arrival_date_day_of_month`
2. Go to **Transform → Merge Columns**.
3. Rename the merged column as **arrival_date**.

---

### Step 3: Create Cost Column

1. Duplicate the **Meal** column.
2. Replace values based on the mapping below:

| Meal      | Cost  |
| --------- | ----- |
| Undefined | 0     |
| BB        | 12.99 |
| HB        | 17.99 |
| FB        | 21.99 |
| SC        | 35    |

3. Rename the column as **cost**.

---

### Step 4: Create Discount Column

1. Duplicate the **market_segment** column.
2. Replace values using the mapping below:

| Market Segment | Discount |
| -------------- | -------- |
| Undefined      | 0        |
| Direct         | 0.1      |
| Groups         | 0.1      |
| Corporate      | 0.15     |
| Aviation       | 0.2      |
| Offline TA/TO  | 0.3      |
| Online TA      | 0.3      |
| Complementary  | 1        |

3. Rename the column as **discount**.

---

### Step 5: Create hotel_id Column

1. Duplicate the **hotel** column.
2. Replace values as shown below:

| Hotel        | hotel_id |
| ------------ | -------- |
| City Hotel   | H02      |
| Resort Hotel | H01      |

3. Rename the column as **hotel_id**.

---

### Step 6: Create room_id Column

1. Duplicate the **reserved_room_type** column.
2. Replace values using the mapping below:

| Reserved Room Type | room_id |
| ------------------ | ------- |
| A                  | 1       |
| B                  | 2       |
| C                  | 3       |
| D                  | 4       |
| E                  | 5       |
| F                  | 6       |
| G                  | 7       |
| H                  | 8       |
| L                  | 9       |
| P                  | 10      |

3. Rename the column as **room_id**.

---

### Step 7: Create booking_id Column

1. Go to **Add Column → Index Column → Custom**.
2. Set starting index = 1 and increment = 1.
3. Rename the column as **booking_id**.

---

### Step 8: Create date_key Column

1. Go to **Add Column → Custom Column** in Power Query Editor.
2. Use the formula:

```
Date.Year([arrival_date]) * 10000 +
Date.Month([arrival_date]) * 100 +
Date.Day([arrival_date])
```

3. Rename the column as **date_key**.

---

## Step 9: Create Dimension Tables

### 9.1 Hotel Dimension

* Duplicate **Raw_fact**
* Keep only `hotel` and `hotel_id`
* Remove remaining columns
* Remove duplicate rows

---

### 9.2 Date Dimension

* Duplicate **Raw_fact**
* Keep only `date_key` and `arrival_date`
* Remove remaining columns
* Remove duplicate rows

---

### 9.3 Room Dimension

* Duplicate **Raw_fact**
* Keep only `reserved_room_type` and `room_id`
* Remove remaining columns
* Remove duplicate rows

---

### 9.4 Customer Dimension

* Duplicate **Raw_fact**
* Keep only `adult`, `children`, `babies`, `country`, `is_repeated_guest`
* Remove duplicates based on `adult`, `children`, `babies`, `country`
* Create **customer_id** using Index Column 

---

## Step 10: Handle Missing Country Values

Identified NULL values in the country column.

Replaced NULL values with Blank.

Removed rows containing blank country values using Remove Blank Rows.

This step ensures data consistency and prevents issues during customer-level analysis and dimension table creation.

---

## Step 11: Star Schema Creation (Model View)

* Navigate to **Model View** in Power BI Desktop
* Create **one-to-many relationships** between:

  * Raw_fact ↔ Hotel Dimension (`hotel_id`)
  * Raw_fact ↔ Date Dimension (`date_key`)
  * Raw_fact ↔ Room Dimension (`room_id`)
  * Raw_fact ↔ Customer Dimension (`customer_id`)
* Ensure **Raw_fact** is the central fact table

---

## Step 12: Additional Derived Columns

### total_nights Column

```
[stays_in_weekend_nights] + [stays_in_week_nights]
```

---

### stay_type Column

```
if [stays_in_weekend_nights] = 2 then "Weekend Stay"
else if [total_nights] = 1 then "Short Stay"
else if [total_nights] >= 4 then "Extended Stay"
else "Standard Stay"
```

---

## Outcome

By the end of this module:

* Raw data is transformed into a clean fact table
* Dimension tables support efficient slicing and filtering
* A well-structured **star schema** is established
* Data is fully prepared for KPI creation and analytical reporting in subsequent modules

---

![alt text](<Screenshot 2026-02-05 204133.png>)

✔ Module 1 successfully establishes the **data foundation and dimensional model** for the entire analytics solution.
