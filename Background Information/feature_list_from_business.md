# Features used in Customer States Definitions

## Requirements

Features should:
* Correlate with *value* in a intuitive way; for example, a feature measuring product depth should correlate with value in a way that marketers could encourage cross-shopping and assume that will lead to higher value.
* Be easy to understand. Stakeholders should understand the underlying behavior that the feature is measuring. For example the *consistancy* features are mathematically complex, but measure *clumpiness*, something all of us can understand.
* Be operationally actionable. Stakeholders should be able to influence the feature by pulling a lever.
* Be indipendent. Features should not measure the same behavior.

Each section below corresponds to a logical feature group.

last updated: June 4

---

# Table of Contents

1. [RFM Features](#1.-RFM-Features)
2. [product preference features](#7.-Product-Preference-Features)
3. [Time of day and weekend features](#3.-Time-of-Day-&-Weekend-Features)
4. [Discount and Star use features](#4.-Discount-and-Star-use-features)
5. [Channel features](#5.-Channel-Features)

# 1. RFM Features

RFM features measure customers' transactional behavior. Within the States framework we want to use the RFM features to order the States from least to most *valueable* customers. We only want to include 2 transactional features. Thus, our approach is to identify the two features that are most predictive of *value*.

| Concept | Feature | 30-days | 90-days | 180-days | 1 year |
|---|---|---|---|---|---|
| Recency | Days since last order |  |  |  | X |
| Frequency | Number of transactions | X | X | X | X |
|  | Number of days with a transaction | X | X | X | X |
|  | Number of weeks with a transaction |  | X | X | X |
| Monetary | Average monthly net revenue | X | X | X | X |
|  | Log average monthly net revenue | X | X | X | X |
| Momentum | $\Delta$ of frequency |  |  |  |  |
| Consistency | $1/(1+\mathrm{CV})$, where $\mathrm{CV}$ = coefficient of variation of inter-purchase dates |  | X | X | X |
|  | Entropy = $\sum(x \cdot \ln(x))$ where $x = \frac{\mathrm{day\_diff}}{\sum(\mathrm{day\_diff})}$ per customer. Higher entropy $\rightarrow$ more evenly spaced transactions. |  | X | X | X |
|  | $\mathrm{second\_moment}$ = second moment of the gap distribution |  | X | X | X |
|  | $\mathrm{log\_utility}$ = log utility of the gap distribution: $\sum(\ln(x))$ |  | X | X | X |


## Consistency Features

| Feature Name | Data Type | Description |
|---|---|---|
| `coefficient of variation` | FLOAT | coefficient of variation of inter-transaction gaps. |
| `entropy` | FLOAT | Shannon-like entropy of inter-transaction gaps. |
| `second_moment` | FLOAT | Concentration of inter-transaction gaps. |
| `log_utility` | FLOAT | Log utility of inter-transaction gap distribution. |
---

### Coefficient of variation 
Coefficient of variation of the inter-purchase dates

$$
\frac{1}{(1=\mathrm{CV})}
$$

Where $\mathrm{CV}$ is the coefficient of variation of $y$, where $y$ is the intertransaction gap (number of days between subsequent orders).

### Entropy
Shannon-like entropy of the inter-transaction gap distribution. Higher entropy means more evenly spaced transactions

$$
\sum (x \times \ln(x))
$$

where $x$ is "gap distribution", 

$$
x = \frac{y}{\sum (y)}
$$

### Second Moment

Also calculated from $x$ above, is the second moment (basically the variance) of the gap distribution. Higher values means more clumpiness.

$$
\sum x^2
$$

### Log Utility

Also calculated from the gap distribution. 

$$
\sum(\ln(x))
$$

# 2. Product Preference Features

| Feature Name | Data Type | Description |
|---|---|---|
| `count_trans_with_food` | INTEGER | Transactions containing food items. |
| `count_trans_with_beverage` | INTEGER | Transactions containing beverages. |
| `avg_food_count` | FLOAT | Avg food items per food transaction. |
| `avg_beverage_count` | FLOAT | Avg beverages per beverage transaction. |
| `frac_modifier_item` | FLOAT | Modifier share of total items. |
| `hot_pct_1yr` | FLOAT | Fraction of beverage purchases that were hot beverages. |
| `beverage_recency` | DATE | Most recent beverage purchase date. |
| `food_recency` | DATE | Most recent food purchase date. |
| `modifier_recency` | DATE | Most recent modifier purchase date. |
---

# 3. Time-of-Day & Weekend Features
Time of day and weekend RFM features measure the recency, frequency, and monetization of daypart and weekend behavior.

| Feature Name | Description |
|---|---|
| `c360_weekend_frequency` | Weekend transaction frequency. |
| `c360_weekend_recency` | Weekend transaction recency. |
| `c360_weekend_AOV` | Weekend transaction average order value. |
| `peak_frequency` | Peak transaction frequency. |
| `peak_recency` | Peak transaction recency. |
| `peak_AOV` | Peak transaction average order value. |
| `afternoon_frequency` | Peak transaction frequency. |
| `afternoon_recency` | Peak transaction recency. |
| `afternoon_AOV` | Peak transaction average order value. |
---

# 4. Discount and Star use features

## Key Features
| Feature Name | Data Type | Description |
|---|---|---|
| `star_balance` | INT | Number of stars earned -- will correlate highly with frequency |
| `star_recency` | INT | Days since most recent star use |
| `star_frequency` | INT | Frequency of star use |
| `star_AOV` | FLOAT | Average order value when using stars |
| `discount_recency` | INT | Days since most recent discount |
| `discount_frequency` | INT | discount frequency |
| `discount_AOV` | FLOAT | Average order value of discounted orders |
| `optin_rate` | FLOAT | Fraction of games opted into. |
| `win_stars_rate` | FLOAT | Fraction of games with stars won. |
| `game_completion_rate` | FLOAT | Fraction of games completed. |
| `email_opens_per_game` | FLOAT | Avg email opens per game. |
| `email_clicks_per_game` | FLOAT | Avg email clicks per game. |
| `moc_views_per_game` | FLOAT | Avg MOC views per game. |
| `moc_taps_per_game` | FLOAT | Avg MOC taps per game. |
---

### Opt-In Rate

$$
\frac{\text{opted in num}}{\text{num games}}
$$

### Win Stars Rate

$$
\frac{\text{num games won stars}}
{\text{num games}}
$$

### Game Completion Rate

$$
\frac{\text{games completed num}}
{\text{num games}}
$$

# 5. Channel Features

| Feature Name | Data Type | Description |
|---|---|---|
| `number of channels used` | INTEGER | Number of different channels used. |
| `Shannon_diversity_channels` | FLOAT | Shannon Diversity index of channel use |
| `Simpsons_diversity_channels` | FLOAT | Simpsons Diversity index of channel use |
| `frequency_OTW` | INTEGER | Transactions out the window. |
| `frequency_MOP` | INTEGER | Transactions Mobile, Order, & Pay. |
| `frequency_cafe` | INTEGER | Transactions Cafe. |
| `recency_OTW` | INTEGER | Days since last OTW order. |
| `recency_MOP` | INTEGER | Days since last MOP order. |
| `recency_cafe` | INTEGER | Days since last cafe order. |
| `AOV_OTW` | FLOAT | average transaction value OTW. |
| `AOV_MOP` | FLOAT | average transaction value MOP. |
| `AOV_Cafe` | FLOAT | average transaction value Cafe. |
---

### Shannon Diversity of channel use
$$ H' = - \sum_1^3 p_i \ln(p_i) $$

where $p_i$ = proportion of transactions (or monetary) belonging in channel $i$.

The index increases when channel use is more evenly distributed.

### Simpson Diversity of channel use
$$ D = 1 - \sum_1^3 p^2_i $$

where $p_i$ = proportion of transactions (or monetary) belonging in channel $i$.

A higher $D$ means more diversity in channel useage.


### Food & Beverage Ratio Features
Upstream task: food_beverage_ratios
Ultimate source tables:

edap_pub_customersales.Customer_Sales_Transaction360_Line_Item (via aggregated_transactions)
edap_pub_productitem.enterprise_product_hierarchy (for product type classification: Food / Beverage / Modifier, via aggregated_transactions)
Note: SVC (Licensed Store) transactions do not have item-level detail and are excluded from all food/beverage ratio calculations (count_* columns are NULL for SVC records in aggregated_transactions).

Feature Name	Data Type	Calculated?	Description / Calculation Summary
count_trans	INTEGER	Yes	Total number of POS transactions in the 8-week window (only transactions with non-null item counts are included).
count_trans_with_food	INTEGER	Yes	Number of transactions containing at least one food item.
count_trans_with_beverage	INTEGER	Yes	Number of transactions containing at least one beverage item.
count_trans_with_modifier	INTEGER	Yes	Number of transactions containing at least one modifier item.
frac_trans_with_food	FLOAT	Yes	count_trans_with_food / count_trans.
frac_trans_with_beverage	FLOAT	Yes	count_trans_with_beverage / count_trans.
frac_trans_with_modifier	FLOAT	Yes	count_trans_with_modifier / count_trans.
total_count_food	INTEGER	Yes	Total number of food items purchased across all transactions.
total_count_beverage	INTEGER	Yes	Total number of beverage items purchased across all transactions.
total_count_modifier	INTEGER	Yes	Total number of modifier items purchased across all transactions.
avg_food_count	FLOAT	Yes	Average number of food items per food-containing transaction: total_count_food / count_trans_with_food.
avg_beverage_count	FLOAT	Yes	Average number of beverage items per beverage-containing transaction: total_count_beverage / count_trans_with_beverage.
frac_modifier_item	FLOAT	Yes	Modifiers as a share of total food + beverage items: total_count_modifier / (total_count_food + total_count_beverage).
count_trans_single_food	INTEGER	Yes	Number of transactions with exactly 1 food item.
count_trans_single_beverage	INTEGER	Yes	Number of transactions with exactly 1 beverage item.
frac_food_trans_single	FLOAT	Yes	Fraction of food-containing transactions with exactly 1 food item: count_trans_single_food / count_trans_with_food.
frac_beverage_trans_single	FLOAT	Yes	Fraction of beverage-containing transactions with exactly 1 beverage item: count_trans_single_beverage / count_trans_with_beverage.
count_trans_food_no_bev	INTEGER	Yes	Number of transactions with food but no beverage items.
frac_food_trans_no_bev	FLOAT	Yes	Fraction of food-containing transactions with no beverage: count_trans_food_no_bev / count_trans_with_food.
count_trans_beverage_no_food	INTEGER	Yes	Number of transactions with beverage but no food items.
frac_beverage_trans_no_food	FLOAT	Yes	Fraction of beverage-containing transactions with no food: count_trans_beverage_no_food / count_trans_with_beverage.
count_trans_single_food_single_beverage	INTEGER	Yes	Number of transactions with exactly 1 food item AND exactly 1 beverage item.
frac_trans_single_food_single_beverage	FLOAT	Yes	count_trans_single_food_single_beverage / count_trans.