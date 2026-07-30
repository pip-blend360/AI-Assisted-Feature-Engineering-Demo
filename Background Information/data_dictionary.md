# Olist E-Commerce Data Dictionary

Brazilian e-commerce dataset from Olist Store. Nine CSV tables in `Data/` covering ~100k orders placed between **2016-09-04** and **2018-10-17**. This dictionary is intended to give an AI agent enough context to engineer features (e.g. customer LTV, churn, review, and delivery-quality features) on top of the raw tables.

## Table overview

| Table | Grain (one row per) | Rows | Primary key |
|---|---|---|---|
| `olist_customers_dataset` | customer (per-order identity) | 99,441 | `customer_id` |
| `olist_orders_dataset` | order | 99,441 | `order_id` |
| `olist_order_items_dataset` | item line within an order | 112,650 | `order_id` + `order_item_id` |
| `olist_order_payments_dataset` | payment installment record | 103,886 | `order_id` + `payment_sequential` |
| `olist_order_reviews_dataset` | review | 99,224 | `review_id` (see notes) |
| `olist_products_dataset` | product | 32,951 | `product_id` |
| `olist_sellers_dataset` | seller | 3,095 | `seller_id` |
| `olist_geolocation_dataset` | lat/lng point | 1,000,163 | none (multiple rows per zip prefix) |
| `product_category_name_translation` | category | 71 | `product_category_name` |

## Entity relationships

```
customers (customer_unique_id = true person)
   │ 1:1 customer_id
orders ──1:N── order_items ──N:1── products ──N:1── category_translation
   │                  │
   │ 1:N              └──N:1── sellers
   ├── payments
   └── reviews

geolocation ── keyed by zip_code_prefix ── customers.customer_zip_code_prefix
                                        └── sellers.seller_zip_code_prefix
```

Key point for customer-level features: `customer_id` is unique **per order**. To track a real person across orders, join through `customer_unique_id`. There are 96,096 unique persons across 99,441 customer/order identities.

---

## olist_orders_dataset
Central order table. One row per order. `order_id` unique.

| Column | Type | Description | Notes |
|---|---|---|---|
| `order_id` | string (hash) | Order identifier. PK. | |
| `customer_id` | string (hash) | FK → `customers.customer_id`. Per-order customer key. | 1:1 with order |
| `order_status` | categorical | Order lifecycle status. | Values: `delivered` (96,478), `shipped` (1,107), `canceled` (625), `unavailable` (609), `invoiced` (314), `processing` (301), `created` (5), `approved` (2). Filter to `delivered` for most revenue/LTV features. |
| `order_purchase_timestamp` | datetime | When the order was placed. | Main event time. Range 2016-09 to 2018-10. |
| `order_approved_at` | datetime | Payment approval time. | 160 blanks |
| `order_delivered_carrier_date` | datetime | Handed to logistics partner. | 1,783 blanks |
| `order_delivered_customer_date` | datetime | Actual delivery to customer. | 2,965 blanks (non-delivered orders) |
| `order_estimated_delivery_date` | datetime | Promised delivery date at purchase. | |

Feature ideas: delivery lateness = `delivered_customer_date − estimated_delivery_date`; processing time = `approved_at − purchase_timestamp`; time-to-delivery; day-of-week / hour / seasonality from `purchase_timestamp`.

## olist_customers_dataset
Customer identity per order plus location.

| Column | Type | Description | Notes |
|---|---|---|---|
| `customer_id` | string (hash) | PK. Per-order key that joins to `orders`. | 99,441 unique |
| `customer_unique_id` | string (hash) | Stable identity of the actual person across orders. | 96,096 unique — **use this for LTV / repeat-purchase / churn** |
| `customer_zip_code_prefix` | string | First 5 digits of zip; FK → `geolocation`. | Leading zeros matter — keep as string |
| `customer_city` | string | City (lowercase, Portuguese). | |
| `customer_state` | categorical | 2-letter Brazilian state. | 27 states; SP dominant |

## olist_order_items_dataset
One row per physical item line in an order. An order with 3 units of the same product yields 3 rows with `order_item_id` 1,2,3.

| Column | Type | Description | Notes |
|---|---|---|---|
| `order_id` | string (hash) | FK → `orders`. | 98,666 distinct orders |
| `order_item_id` | int | Sequential line number within the order (1..N). | Also serves as item quantity indicator |
| `product_id` | string (hash) | FK → `products`. | |
| `seller_id` | string (hash) | FK → `sellers`. | |
| `shipping_limit_date` | datetime | Seller's shipping deadline. | |
| `price` | float | Item price (BRL). | Range 0.85 – 6,735.00 |
| `freight_value` | float | Shipping cost allocated to the item (BRL). | |

Feature ideas: order revenue = sum(`price`); order value including freight = sum(`price`+`freight_value`); items per order; distinct products/sellers per order; basket-level product mix.

## olist_order_payments_dataset
One row per payment record; an order can have multiple (e.g. split across vouchers + card).

| Column | Type | Description | Notes |
|---|---|---|---|
| `order_id` | string (hash) | FK → `orders`. | |
| `payment_sequential` | int | Sequence of payment within order. | |
| `payment_type` | categorical | Payment method. | `credit_card` (76,795), `boleto` (19,784), `voucher` (5,775), `debit_card` (1,529), `not_defined` (3) |
| `payment_installments` | int | Number of installments. | 0 – 24 |
| `payment_value` | float | Amount for this payment record (BRL). | |

Note: sum of `payment_value` per order ≈ item price + freight, but not always exact. For revenue prefer order_items; for payment-behavior features use this table.

## olist_order_reviews_dataset
Customer satisfaction reviews. One review per order in most cases.

| Column | Type | Description | Notes |
|---|---|---|---|
| `review_id` | string (hash) | Review identifier. | 98,410 unique across 99,224 rows — **not fully unique**; some reviews span multiple orders. Deduplicate before joining. |
| `order_id` | string (hash) | FK → `orders`. | 98,673 distinct |
| `review_score` | int 1–5 | Star rating. | Distribution: 5★ 57,328 / 4★ 19,142 / 3★ 8,179 / 2★ 3,151 / 1★ 11,424 |
| `review_comment_title` | string | Optional title. | 87,656 blank |
| `review_comment_message` | string | Optional free text (Portuguese). | 58,247 blank |
| `review_creation_date` | datetime | When the review survey was sent. | |
| `review_answer_timestamp` | datetime | When the customer answered. | |

Feature ideas: avg review score per customer, share of low (1–2) scores, has-comment flag, review response latency.

## olist_products_dataset
Product catalog and physical attributes.

| Column | Type | Description | Notes |
|---|---|---|---|
| `product_id` | string (hash) | PK. | 32,951 |
| `product_category_name` | categorical | Category in Portuguese; FK → `product_category_name_translation`. | 74 categories; 610 rows blank |
| `product_name_lenght` | int | Char length of product name. | Original misspelling "lenght" kept |
| `product_description_lenght` | int | Char length of description. | |
| `product_photos_qty` | int | Number of published photos. | |
| `product_weight_g` | int | Weight in grams. | |
| `product_length_cm` / `product_height_cm` / `product_width_cm` | int | Package dimensions in cm. | Useful for freight/volume features |

## olist_sellers_dataset
Seller registry and location.

| Column | Type | Description | Notes |
|---|---|---|---|
| `seller_id` | string (hash) | PK. | 3,095 |
| `seller_zip_code_prefix` | string | Zip prefix; FK → `geolocation`. | Keep as string |
| `seller_city` | string | City. | |
| `seller_state` | categorical | 2-letter state. | |

Feature ideas: customer↔seller distance (via geolocation lat/lng), same-state flag, seller concentration per customer.

## olist_geolocation_dataset
Lat/lng lookup keyed by zip prefix. **Not unique** — many points per prefix (19,015 distinct prefixes, ~1M rows). Aggregate (e.g. mean lat/lng) per prefix before joining.

| Column | Type | Description |
|---|---|---|
| `geolocation_zip_code_prefix` | string | 5-digit zip prefix (join key) |
| `geolocation_lat` | float | Latitude |
| `geolocation_lng` | float | Longitude |
| `geolocation_city` | string | City |
| `geolocation_state` | categorical | 2-letter state |

## product_category_name_translation
Maps Portuguese category names to English. 71 rows.

| Column | Type | Description |
|---|---|---|
| `product_category_name` | string | Portuguese name (join key → `products`). First column has a UTF-8 BOM — strip it on load. |
| `product_category_name_english` | string | English label |

---

## Notes for feature engineering
- **Customer grain:** aggregate to `customer_unique_id`, not `customer_id`, for LTV/repeat/churn. Path: `customer_unique_id → customers.customer_id → orders → order_items/payments/reviews`.
- **Money:** all values in Brazilian Real (BRL). Revenue = `order_items.price`; total paid = price + freight or `payments.payment_value`.
- **Time:** use `order_purchase_timestamp` as the event clock. Dataset window 2016-09 to 2018-10 (2016 is sparse). Define observation/label windows carefully to avoid leakage.
- **Order filtering:** restrict to `order_status = 'delivered'` for realized revenue; keep other statuses only for cancellation/failure features.
- **Join hygiene:** dedupe `reviews` by `review_id`; aggregate `geolocation` per zip prefix; treat all zip prefixes and IDs as strings; handle blank category names and blank delivery dates as missing.
