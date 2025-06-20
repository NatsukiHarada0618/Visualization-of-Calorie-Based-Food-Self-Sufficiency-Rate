# Visualization of Calorie-Based Food Self-Sufficiency Rate

---

## 1. Overview

### 1.1 Objective

* Understand the structural causes of Japan's low calorie-based food self-sufficiency rate and compare it with other regions
* Focus specifically on **wheat**, and analyze the historical trend in Japan’s self-sufficiency rate

### 1.2 Target Regions

* World, Japan, Europe, Oceania, Asia, North America

### 1.3 Target Items (16 items)

* Wheat, rice, barley, maize, potatoes, soybeans, vegetables, beef, pork, eggs, milk, poultry, lentils, sugar, soybean oil, fish

### 1.4 Time Period

* 1961–2013

---

## 2. Project Files

| Filename       | Description                      |
| -------------- | -------------------------------- |
| `README.ipynb` | Explanatory notebook (this file) |
| `Self-Sufficiency Rate.ipynb`  | Main visualization notebook      |
| `Self-Sufficiency Rate.xlsx`   | Raw dataset                      |
| `Output/`          | Folder containing output graphs  |

---

## 3. Data Sources

* Source 1: [FAOSTAT](https://www.fao.org/faostat/)
* Source 2: [USDA FoodData Central](https://www.usda.gov/)

---

## 4. How to Run

1. Required Python libraries:

   * `numpy`
   * `pandas`
   * `matplotlib.pyplot`
   * `seaborn`
   * `random`

2. Run `Self-Sufficiency Rate.ipynb` in Jupyter Notebook

3. Place `Self-Sufficiency Rate.xlsx` in the same directory as the notebook

---

## 5. Key Findings

* **Japan's calorie-based food self-sufficiency rate is significantly lower than that of other regions**, both in total (16 items) and for wheat alone
* Particularly for wheat, **Japan’s self-sufficiency remains in the single digits**, in stark contrast to major exporting countries in Oceania and North America

---

## 6. Notes

* The items analyzed are limited, and calorie conversion coefficients are based on USDA data. Therefore, the results **may differ from those published by Japan’s Ministry of Agriculture, Forestry and Fisheries (MAFF)**
* This project is intended for **international comparison and trend analysis**, not direct policy evaluation

---

## 7. Output Folder Structure (Graph Images)

* `Calorie_Based_Food_Self_Sufficiency_Rate_Trend.png`
* `Wheat_Calorie_Based_Self_Sufficiency_Trend.png`

---

## 8. Food Category Mapping Table (FAOSTAT × USDA)

| FAOSTAT Item Name          | Corresponding USDA Food Name                                              | Calorie Coefficient (kcal/100g) |
| -------------------------- | ------------------------------------------------------------------------- | ------------------------------- |
| Wheat and products         | Wheat flour, whole-grain, soft wheat                                      | 332                             |
| Rice (Milled Equivalent)   | Rice, white, long-grain, regular, raw, unenriched                         | 365                             |
| Bovine Meat                | Beef, loin, tenderloin steak, boneless, lean only, trimmed to 0" fat, raw | 143                             |
| Pigmeat                    | Pork, fresh, loin, tenderloin, lean only, raw                             | 109                             |
| Poultry Meat               | Chicken, broiler, breast, skinless, boneless, raw                         | 120                             |
| Milk - Excluding Butter    | Milk, whole, 3.25% milkfat, with added vitamin D                          | 61                              |
| Eggs                       | Egg, whole, raw, fresh                                                    | 143                             |
| Soyabean Oil               | Oil, soybean, refined                                                     | 884                             |
| Sugar (Raw Equivalent)     | Sugars, granulated                                                        | 387                             |
| Vegetables, other          | Vegetables, mixed, frozen, unprepared                                     | 72                              |
| Potatoes and products      | Potatoes, flesh and skin, raw                                             | 77                              |
| Maize and products         | Cornmeal, degermed, enriched, yellow                                      | 370                             |
| Barley and products        | Barley, hulled, raw                                                       | 354                             |
| Pulses, Other and products | Lentils, raw                                                              | 352                             |
| Pelagic Fish               | Fish, salmon, Atlantic, wild, raw                                         | 142                             |
| Soybeans                   | Soybeans, mature seeds, raw                                               | 446                             |


---


## 9. Method of Calculating Calorie-Based Food Self-Sufficiency Rate (FSSR)

### 1. Definition

The Food Self-Sufficiency Rate (FSSR) is an indicator that shows what percentage of food consumed domestically is covered by **domestic production**, expressed in **calorie terms**.

#### Basic Formula

$$
\text{FSSR (\%)} = \left( \frac{\text{Domestic Calorie Supply (kcal/capita/day)}}{\text{Total Food Supply (kcal/capita/day)}} \right) \times 100
$$

---

### 2. Numerator: Domestic Calorie Supply (Adjusted for Edible Quantity)

Based on `Production` data from FAOSTAT, the following items are subtracted or adjusted to estimate the **edible portion of food production** (adjusted production):

```python
adjusted_food_1000t = (
    Production
    - Feed
    - Seed
    - Losses
    - Other Uses (non-food)
    - Processed
    - Export Quantity
    + Stock Variation
)
```

This is then converted into calories and standardized per capita:


Numerator = Σ [adjusted_food_1000tᵢ × 1,000,000 × kcal/kgᵢ] ÷ (Population × 365)


* `adjusted_food_1000t_i`: Edible portion of item *i* (in 1,000 tons)
* `kcal/kg_i`: Calorie conversion coefficient for item *i* (based on USDA data)
* `Population × 365`: Converts to per capita, per day basis (kcal/capita/day)

---

### 3. Denominator: Total Food Supply

Directly use the FAOSTAT item `Food supply (kcal/capita/day)`:

* Extract all rows where `Element == "Food supply (kcal/capita/day)"`
* If multiple items exist, sum across items to get the total `kcal_supply`

---

### 4. Calorie Conversion Coefficients

Calorie coefficients are retrieved from USDA FoodData Central or the USDA Standard Reference and matched with FAOSTAT item categories:

* Example: Wheat (Wheat and products): 332 kcal/100g = 3,320 kcal/kg
* For the full list, see the separate document "FAOSTAT × USDA Food Category Mapping Table"

---

### 5. Implementation Notes

* All data is processed on a yearly basis and aggregated by `Area × Year`

---

### 6. Remarks

* This calculation is based on **Food Balance Sheets (FBS)** with a focus on **international comparability**
