# カロリーベース食料自給率の可視化

---

## 1. 概要

### 1.1 目的

* 日本のカロリーベース食料自給率の構造的な低さを把握し、他地域と比較する
* 特に **小麦** に着目し、自給率の推移を把握する

### 1.2 対象地域

* 世界、日本、欧州、オセアニア、アジア、北アメリカ

### 1.3 対象品目（16品目）

* 小麦、米、大麦、トウモロコシ、ジャガイモ、大豆、野菜、牛肉、豚肉、卵、牛乳、鶏肉、レンズ豆、砂糖、大豆油、魚類

### 1.4 対象期間

* 1961年～2013年

---

## 2. 構成ファイル

| ファイル名          | 内容               |
| -------------- | ---------------- |
| `README.ipynb` | 説明用ノートブック（本ファイル） |
| `食料自給率.ipynb`  | 可視化の本体           |
| `食料自給率.xlsx`   | 元データ             |
| `結果/`          | 出力されたグラフ         |

---

## 3. 使用データ

* 出共1: [FAOSTAT](https://www.fao.org/faostat/)
* 出共2: [USDA FoodData Central](https://www.usda.gov/)

---

## 4. 実行方法

1. 必要ライブラリ：

   * `numpy`
   * `pandas`
   * `matplotlib.pyplot`
   * `seaborn`
   * `random`

2. `食料自給率.ipynb` を Jupyter 上で実行

3. `食料自給率.xlsx` をノートブックと同じ階層に配置すること

---

## 5. 結果概要

* **日本はカロリーベース食料自給率において、16品目合計でも、小麦単独でも他地域より著しく低い**
* 特に小麦では、オセアニア・北米の主要輸出国と比較して **自給率が一桁台で推移**

---

## 6. 注意事項

* 使用した品目は限定的であり、またカロリー係数は USDA を参照しているため、**農林水産省が公表する自給率と異なる可能性がある**
* 本データは **国際比較による傾向把握** を目的としており、国内政策評価と一致するとは限らない

---

## 7. 結果フォルダ構成（出力画像）

* `結果/カロリーベースの食料自給率の年次推移.png`
* `結果/小麦のカロリーベース食料自給率の年次推移.png`


---


## 8. 食品カテゴリ対応表（FAOSTAT × USDA）

| FAOSTAT品目名                 | 対応するUSDA食品名                                                                      | カロリー係数（kcal/100g） |
| -------------------------- | ----------------------------------------------------------------------------------- | ----------------- |
| Wheat and products         | Wheat flour, whole-grain, soft wheat                                                | 332               |
| Rice (Milled Equivalent)   | Rice, white, long-grain, regular, raw, unenriched                                   | 365               |
| Bovine Meat                | Beef, loin, tenderloin steak, boneless, separable lean only, trimmed to 0" fat, raw | 143               |
| Pigmeat                    | Pork, fresh, loin, tenderloin, separable lean only, raw                             | 109               |
| Poultry Meat               | Chicken, broiler or fryers, breast, skinless, boneless, meat only, raw              | 120               |
| Milk - Excluding Butter    | Milk, whole, 3.25% milkfat, with added vitamin D                                    | 61                |
| Eggs                       | Egg, whole, raw, fresh                                                              | 143               |
| Soyabean Oil               | Oil, vegetable, soybean, refined                                                    | 884               |
| Sugar (Raw Equivalent)     | Sugars, granulated                                                                  | 387               |
| Vegetables, other          | Vegetables, mixed, frozen, unprepared                                               | 72                |
| Potatoes and products      | Potatoes, flesh and skin, raw                                                       | 77                |
| Maize and products         | Cornmeal, degermed, enriched, yellow                                                | 370               |
| Barley and products        | Barley, hulled, raw                                                                 | 354               |
| Pulses, Other and products | Lentils, raw                                                                        | 352               |
| Pelagic Fish               | Fish, salmon, Atlantic, wild, raw                                                   | 142               |
| Soybeans                   | Soybeans, mature seeds, raw                                                         | 446               |


---


## 9. カロリーベース食料自給率の計算方法（FSSR: Food Self-Sufficiency Rate）

### 1. 定義

カロリーベース食料自給率（FSSR）は、国内で消費される食料のうち、どれだけが**国内生産**で賄われているかを**カロリー換算**で示す指標です。

#### 数式（基本形）

$$
\text{FSSR (\%)} = \left( \frac{\text{国内供給カロリー（kcal/capita/day）}}{\text{総食料供給カロリー（kcal/capita/day）}} \right) \times 100
$$

---

### 2. 分子：国内供給カロリー（実質的に食用可能なカロリー量）

FAOSTATの `Production` を基に、以下の要素を控除・調整し、**実質的に食用に回ると推定される量**（adjusted production）を計算：

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

これをカロリー換算して、人口で標準化：

分子 = Σ [adjusted_food_1000tᵢ × 1,000,000 × kcal/kgᵢ] ÷ (人口 × 365)

* `adjusted_food_1000t_i`: 品目 *i* の実質食用可能量（1000トン）
* `kcal/kg_i`: 品目 *i* のカロリー換算係数（USDA基準）
* `人口 × 365`: 年間・1人あたり・日単位で換算（kcal/capita/day）

---

### 3. 分母：総食料供給カロリー

FAOSTATの `Food supply (kcal/capita/day)` をそのまま使用：

* 各国・年・地域の `Element == "Food supply (kcal/capita/day)"` を抽出し、合計
* 複数品目がある場合は品目別値を合計して `kcal_supply` として使用

---

### 4. カロリー換算係数について

USDA FoodData CentralまたはStandard Referenceから取得し、FAOSTATの品目分類に対応づけて使用：

* 例）小麦（Wheat and products）：332 kcal/100g = 3,320 kcal/kg
* すべての係数一覧は別紙「食品カテゴリ対応表（FAOSTAT × USDA）」参照

---

### 5. 実装上の注意

* すべてのデータは年次ベースで処理し、`Area × Year` ごとに集計

---

### 6. 備考

* 本計算は**国際比較可能性を重視したFBS（Food Balance Sheets）ベースの推計**である
