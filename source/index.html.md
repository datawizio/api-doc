---
title: Use an API

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Kittn API
---

# Introduction

Description of use datawiz API. You need to provide your own BES account information. If you do not have an account, contact the administrator to create one.

# Authentication

To begin, you need to get an access token to access the desired application.

```shell
curl 'https://bes.datawiz.io/o/token/' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'client_id=<your_client_id>' \
--data-urlencode 'client_secret=<your_client_secret>' \
--data-urlencode 'username=<your_email>' \
--data-urlencode 'password=<your_password>' \
--data-urlencode 'grant_type=password'
```
> The above command returns JSON structured like this:

```json
{
    "access_token": "accessTokenString",
    "expires_in": 36000,
    "token_type": "Bearer",
    "scope": "read write",
    "refresh_token": "refreshTokenString"
}
```
### HTTP Request

`POST https://bes.datawiz.io/o/token/`

### Query Parameters

`client_id: <your_client_id>`

`client_secret: <your_client_secret>`

`username: <your_email>`

`password: <your_password>`

`grant_type: password`

<aside class="notice"> You must replace <code>&lt;your_client_id&gt;</code> with allowed client_id.
You must replace <code>&lt;your_client_secret&gt;</code> with allowed client_secret.
You must replace <code>&lt;your_email&gt;</code> with your personal e-mail address.
You must replace <code>&lt;your_password&gt;</code> with your personal password.
</aside>

In the future we will use the obtained `token_type` and `access_token` to access the desired application.

# Getting data

### HTTP Request

`POST https://api-new.datawiz.io/api/builder/create-table/`

### HTTP header
`Authorization: <token_type> <access_token>`

`client-id: <client_id>`

`Content-Type: application/json`

<aside class="notice"> Not to be confused with <code>&lt;client_id&gt;</code> for authorization. This  <code>&lt;client-id&gt;</code> is number, its value must be provided to you by the administrator.</aside>

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
date_range | {"selected": "last_7_days"} | Dictionary of key `selected`. If choose value `date` for selected - the data will be sorted for the specified period: {selected: "date", date_from: "<`year_number`>-<`month_number`>-<`day_number`>", date_to: "<`year_number`>-<`month_number`>-<`day_number`>"}.
prev_date_range | {"selected": "previous"} | Dictionary of key `selected`. If choose value `date` for selected - the data will be sorted for the specified period: {selected: "date", date_from: "<`year_number`>-<`month_number`>-<`day_number`>", date_to: "<`year_number`>-<`month_number`>-<`day_number`>"}.
aggregate | [ ] | List of dictionary metric keys. Contains desirable metric keys.
group_by | [ ] | List of dictionary with required keys: dimension, display_fields, lookups, on. Contains required info about dimension by which the data must be grouped.
filters | [ ] | List of dictionary with required keys: dimension, lookups. Contains required info about dimension by which the data must be filtered.
options | {use_cache: true, fixed_total: true} |  List of dictionary with required keys: use_cache, fixed_total.  Additional keys: expand_tree, with_col_total, with_row_total.
render_options | {additional_column_options: {}, clone_data: {name: "chain__name", has_children: "chain__has_children"}, dtype: "table", key_start: "chain="} | List of dictionary with required keys: additional_column_options, additional_data, clone_data, dtype, key_start.

### DateRange Parameters

Parameter | Default | Description
--------- | ------- | -----------
selected | "`selected_key`" | String of selected key. For example: {metric: "last_7_days"}

[Table of selected keys](/api-doc/#table-of-selected-keys-for-date-range).

### Previous DateRange Parameters

Parameter | Default | Description
--------- | ------- | -----------
selected | "`selected_key`" | String of selected key. For example: {metric: "previous"}

[Table of selected keys](/api-doc/##table-of-selected-keys-for-previous-date-range).

### Aggregate Parameters

Parameter | Default | Description
--------- | ------- | -----------
metric | "`metric_key`" | String of metric key. For example: {metric: "turnover"}

### GroupBy Parameters

Parameter | Default | Description
--------- | ------- | -----------
dimension | "`dimension_key`" | String of dimension name. For example: {dimension: "category"}
display_fields | [ "`field_key`" ] | String of field name. For example: {display_fields: [ "name" ]}
lookups| {} | Dictionary of include/exclude key: contains list of ids dimension items; `include` - means that from all data are selected only those id specified in the list, `exclude` - not selected from all data. For example: {include: [ 1, 2, 3 ]}
on | `on_key` | String of on key. Determines location of the dimension in the table or the axis. `row` - dimension to be in row or X axis, `column` - dimension to be in column or Y axis.

[Table of dimension keys](/api-doc/#table-of-dimension-keys).

### Filter Parameters

Parameter | Default | Description
--------- | ------- | -----------
dimension | "`dimension_key`" | String of dimension name. For example: {dimension: "category"}
lookups| {} | Dictionary of include/exclude key: contains list of ids dimension items; `include` - means that from all data are selected only those id specified in the list, `exclude` - not selected from all data. For example: {include: [ 1, 2, 3 ]}

### Option Parameters
Parameter | Default | Description
--------- | ------- | -----------
use_cache | True | Depending on the value (`True`/`False`) - caches/doesn't cache the received data.
expand_tree | False | indicates roll down of the dimension access.
with_col_total | False | If the dimension is in columns, then for dimension is possible to deduce a line of result having changed `with_col_total` to True.
with_row_total | False | If the dimension is in rows, then for dimension is possible to deduce a line of result having changed `with_row_total` to True.

### RenderOption Parameters

<aside class="notice">
Depending on what you want to get - a table or a chart - the required keys will be different. Enough for the <code>Chart</code>: <code>clone_data</code>, <code>dtype</code>. For the <code>Table</code>: <code>additional_column_options</code>, <code>additional_data</code>, <code>clone_data</code>, <code>dtype</code>, <code>key_start</code>.
</aside>

Parameter | Default | Description
--------- | ------- | -----------
additional_column_options | {} | Column extension information.
additional_data | {} | Additional data about dimension, always its includes existence children info.
clone_data | {"name": "`dimension`__ name", "has_children": "`dimension`__has_children""} | Information about the data you want to copy to another key.
dtype | "`dtype_key`" | String of dtype key. Indicates the type of visualization, possible dtype_key: `table`, `chart`.
key_start | " " | Shows the path of the tree roll down. For example: "shop=1&category=6779&product="

# Getting data from reports

### HTTP Request

`POST : https://bi-new.datawiz.io/api/report/reports/<report_slug>/widgets/<widget_slug>/data/`

<aside class="notice">
<code>&lt;report_slug&gt;</code> is the mark of a specific report.
<code>&lt;widget_slug&gt;</code> is the mark of a specific report widget (visualization).
</aside>
[Table of reports and report widgets](/api-doc/#table-of-reports-and-report-widgets).

### HTTP header
`Authorization: <token_type> <access_token>`

`client-id: <client_id>`

`Content-Type: application/json`

<aside class="notice"> Not to be confused with <code>&lt;client_id&gt;</code> for authorization. This  <code>&lt;client-id&gt;</code> is number, its value must be provided to you by the administrator.</aside>

### Query Parameters

Parameter | Description
--------- | -----------
date_range | {from: "<`year_number`>-<`month_number`>-<`day_number`>", to: "<`year_number`>-<`month_number`>-<`day_number`>"}
prev_date_range| {from: "<`year_number`>-<`month_number`>-<`day_number`>", to: "<`year_number`>-<`month_number`>-<`day_number`>"}
metrics | List of String with metric key. For example: [ "turnover" ]

Each report has a number of filters that can be specified in the parameters when getting data from the report. Some of them will be listed in the following table.

<aside class="warning"> Warning: only some reports support next filters!</aside>

Filter key | Description
---------  | -----------
shops      | List of shop ids
categories | List of category ids
products   | List of product ids
group_by_level | Displays the report at a certain level. The level is indicated dimension key, for example: "category".
product_marker| List of product marker ids
receipt_marker | List of receipt marker ids
week_day | list with numbers of days in the week
interval | String of interval key. Possible values: day_month_year, week, month


<aside class="notice">Contact your administrator for detailed information on the report.</aside>

# Examples of getting a date

```shell
Example 1
curl --location --request POST 'https://bi-new.datawiz.io/api/builder/create-table/' \
--header 'Authorization: Bearer oi1d7vAJMIPFiv2rWT1OlSpCCbA0k2' \
--header 'client-id: 313' \
--header 'Content-Type: application/json' \
--data-raw '{
    "date_range": {
        "selected": "date",
        "date_from": "2020-01-01",
        "date_to": "2022-02-16"
    },
    "prev_date_range": {
        "selected": "previous"
    },
    "aggregate": [
        {
            "metric": "plan_turnover_complete_percent"
        },
        {
            "metric": "plan_products_qty_complete_percent"
        },
        {
            "metric": "plan_profit_complete_percent"
        },
        {
            "metric": "plan_margin_price_complete_percent"
        },
        {
            "metric": "plan_marga_complete_percent"
        }
    ],
    "group_by": [
        {
            "dimension": "shop",
            "display_fields": [
                "name"
            ],
            "on": "row",
            "lookups": {}
        }
    ],
    filters: [{dimension: "category", lookups: {include: [5633]}}]
    "options": {
        "use_cache": true,
        "fixed_total": true
    },
    "render_options": {
        "dtype": "table",
        "key_start": "shop=",
        "additional_data": {
            "level": 1,
            "is_root": true
        },
        "clone_data": {
            "name": "shop_name",
            "has_children": "shop_has_children"
        },
        "additional_column_options": {}
    }
}
```
> The above command returns JSON structured like this:

```json
{
    "count": 2,
    "next": null,
    "previous": null,
    "results": {
        "dataSource": [
            {
                "shop__name": "Store #1",
                "plan_turnover_complete_percent": 20.556,
                "plan_products_qty_complete_percent": 21.798,
                "plan_profit_complete_percent": 19.578,
                "plan_margin_price_complete_percent": 94.227,
                "plan_marga_complete_percent": 95.24,
                "level": 1,
                "is_root": true,
                "key": "shop=1"
            },
            {
                "shop__name": "Store #2",
                "plan_turnover_complete_percent": 20.533,
                "plan_products_qty_complete_percent": 20.395,
                "plan_profit_complete_percent": 20.399,
                "plan_margin_price_complete_percent": 99.228,
                "plan_marga_complete_percent": 99.347,
                "level": 1,
                "is_root": true,
                "key": "shop=2"
            }
        ],
        "columns": [
            {
                "key": "shop__name",
                "dataIndex": "shop__name",
                "title": "Shop Name",
                "sorter": true,
                "dtype": "string",
                "parent_key": null,
                "default_visible": true
            },
            {
                "key": "plan_turnover_complete_percent",
                "dataIndex": "plan_turnover_complete_percent",
                "title": "% completed Planned Sales",
                "sorter": true,
                "dtype": "number",
                "parent_key": null,
                "default_visible": true
            },
            {
                "key": "plan_products_qty_complete_percent",
                "dataIndex": "plan_products_qty_complete_percent",
                "title": "% completed Planned Sales Qty",
                "sorter": true,
                "dtype": "number",
                "parent_key": null,
                "default_visible": true
            },
            {
                "key": "plan_profit_complete_percent",
                "dataIndex": "plan_profit_complete_percent",
                "title": "% completed Planned Profit",
                "sorter": true,
                "dtype": "number",
                "parent_key": null,
                "default_visible": true
            },
            {
                "key": "plan_margin_price_complete_percent",
                "dataIndex": "plan_margin_price_complete_percent",
                "title": "Planed Margin Price completed percent",
                "sorter": true,
                "dtype": "number",
                "parent_key": null,
                "default_visible": true
            },
            {
                "key": "plan_marga_complete_percent",
                "dataIndex": "plan_marga_complete_percent",
                "title": "Planed Margin completed percent",
                "sorter": true,
                "dtype": "number",
                "parent_key": null,
                "default_visible": true
            }
        ]
    }
}
```

### Example 1

__Sales plan__. We form a sample of stores sorted by category to obtain indicators "% of the plan". We'll get the following metrics: _% completed Planned Sales_ (`plan_turnover_complete_percent`), _% completed Planned Sales Qty_ (`plan_products_qty_complete_percent`), _% completed Planned Profit_ (`plan_profit_complete_percent`), _Planed Margin Price completed percent_ (`plan_margin_price_complete_percent`),	_Planed Margin completed percent_ (`plan_marga_complete_percent`).


```shell
Example #2
curl --location --request POST 'https://bi-new.datawiz.io/api/builder/create-table/' \
--header 'Authorization: Bearer oN8ojLOFEYz4dsagdAHJkHc618xwy0' \
--header 'client-id: 313' \
--header 'Content-Type: application/json' \
--header 'Cookie: crisp-client%2Fsession%2F0fedd57b-c655-402e-b6f9-502131085dc9=session_e98342c9-9064-48ab-ad84-d2c33c7048a7' \
--data-raw '{
    "date_range": {
        "selected": "date",
        "date_from": "2020-01-01",
        "date_to": "2022-02-16"
    },
    "prev_date_range": {
        "selected": "previous"
    },
    "aggregate": [
        {
            "metric": "losses_total_price_percent_part"
        }
    ],
    "group_by": [
        {
            "dimension": "shop",
            "display_fields": [
                "name"
            ],
            "on": "row",
            "lookups": {}
        }
    ],
    "filters": [
        {
            "dimension": "category",
            "lookups": {
                "include": [
                    5633
                ]
            }
        }
    ],
    "options": {
        "use_cache": true,
        "fixed_total": true
    },
    "render_options": {
        "dtype": "table",
        "key_start": "shop=",
        "additional_data": {
            "children": [],
            "level": 1,
            "is_root": true
        },
        "clone_data": {
            "name": "shop__name",
            "has_children": "shop__has_children"
        },
        "additional_column_options": {}
    }
}
```
> The above command returns JSON structured like this:

```json
{
    "count": 2,
    "next": null,
    "previous": null,
    "results": {
        "dataSource": [
            {
                "shop__name": "Store #1",
                "losses_total_price_percent_part": 2.559,
                "children": [],
                "level": 1,
                "is_root": true,
                "key": "shop=1",
                "name": "Store #1"
            },
            {
                "shop__name": "Store #2",
                "losses_total_price_percent_part": 0.527,
                "children": [],
                "level": 1,
                "is_root": true,
                "key": "shop=3",
                "name": "Store #2"
            }
        ],
        "columns": [
            {
                "key": "shop__name",
                "dataIndex": "shop__name",
                "title": "Shop Name",
                "sorter": true,
                "dtype": "string",
                "parent_key": null,
                "default_visible": true
            },
            {
                "key": "losses_total_price_percent_part",
                "dataIndex": "losses_total_price_percent_part",
                "title": "% of Write-offs in monetary units",
                "sorter": true,
                "dtype": "number",
                "parent_key": null,
                "default_visible": true
            }
        ]
    }
}
```

### Example 2
__Losses__. We form a sample of stores sorted by category - "% of losses". We'll get the following metric: % of Write-offs in monetary units (`losses_total_price_percent_part`).

```shell
Example #3
curl --location --request POST 'https://bi-new.datawiz.io/api/builder/create-table/' \
--header 'Authorization: Bearer oi1d7vAJMIPFiv2rWT1OlSpCCbA0k2' \
--header 'client-id: 313' \
--header 'Content-Type: application/json' \
--data-raw '{
    "date_range": {
        "selected": "date",
        "date_from": "2020-01-01",
        "date_to": "2022-02-16"
    },
    "prev_date_range": {
        "selected": "prev_last_week"
    },
    "aggregate": [
        {
            "metric": "plan_turnover_complete_percent"
        },
        {
            "metric": "plan_profit_complete_percent"
        },
        {
            "metric": "losses_total_price_percent_part"
        }
    ],
    "group_by": [
        {
            "dimension": "categorical_manager",
            "display_fields": [
                "name"
            ],
            "on": "row",
            "lookups": {}
        }
    ],
    "filters": [],
    "options": {
        "use_cache": true,
        "fixed_total": true
    },
    "render_options": {
        "dtype": "table",
        "key_start": "categorical_manager=",
        "additional_data": {
            "level": 1,
            "is_root": true
        },
        "clone_data": {
            "name": "categorical_manager__name",
            "has_children": "categorical_manager__has_children"
        },
        "additional_column_options": {}
    }
}
```
> The above command returns JSON structured like this:

```json
{
    "count": 3,
    "next": null,
    "previous": null,
    "results": {
        "dataSource": [
            {
                "categorical_manager__name": "Blake Chloe",
                "plan_turnover_complete_percent": 3.289,
                "plan_profit_complete_percent": 2.692,
                "losses_total_price_percent_part": 17.012,
                "level": 1,
                "is_root": true,
                "key": "categorical_manager=3",
                "name": "Blake Chloe"
            },
            {
                "categorical_manager__name": "Gillmore Thomas",
                "plan_turnover_complete_percent": 3.517,
                "plan_profit_complete_percent": 3.374,
                "losses_total_price_percent_part": 4.003,
                "level": 1,
                "is_root": true,
                "key": "categorical_manager=2",
                "name": "Gillmore Thomas"
            },
            {
                "categorical_manager__name": "Addams David",
                "plan_turnover_complete_percent": 4.038,
                "plan_profit_complete_percent": 2.543,
                "losses_total_price_percent_part": 13.038,
                "level": 1,
                "is_root": true,
                "key": "categorical_manager=1",
                "name": "Addams David"
            }
        ],
        "columns": [
            {
                "key": "categorical_manager__name",
                "dataIndex": "categorical_manager__name",
                "title": "Category manager Name",
                "sorter": true,
                "dtype": "string",
                "parent_key": null,
                "default_visible": true
            },
            {
                "key": "plan_turnover_complete_percent",
                "dataIndex": "plan_turnover_complete_percent",
                "title": "% completed Planned Sales",
                "sorter": true,
                "dtype": "number",
                "parent_key": null,
                "default_visible": true
            },
            {
                "key": "plan_profit_complete_percent",
                "dataIndex": "plan_profit_complete_percent",
                "title": "% completed Planned Profit",
                "sorter": true,
                "dtype": "number",
                "parent_key": null,
                "default_visible": true
            },
            {
                "key": "losses_total_price_percent_part",
                "dataIndex": "losses_total_price_percent_part",
                "title": "% of Write-offs in monetary units",
                "sorter": true,
                "dtype": "number",
                "parent_key": null,
                "default_visible": true
            }
        ]
    }
}
```

### Example 3
__Categorical Managers__. We form a sample of categorical managers to obtain indicators "% of the plan". We'll get the following metrics: _% completed Planned Sales_ (`plan_turnover_complete_percent`), _% completed Planned Profit_ (`plan_profit_complete_percent`), 	% of Write-offs in monetary units (`losses_total_price_percent_part`).

# Tables of keys

### Table of selected keys for date range
Key       | Description
--------- | -----------
last_update_date | Last available date
penultimate_update_date | Penultimate available date
last_7_days | Last 7 days
last_30_days | Last 30 days
last_90_days | Last 90 Days
last_180_days |	Last 180 Days
last_365_days |	Last year
week_begin | From beginning of the week
month_begin |	From beginning of the month
quarter_begin | From beginning of the quarter
year_begin | From beginning of the quarter
prev_week | Previous week
prev_month | Previous month
prev_quarter | Previous month
prev_year | Previous year
all_time| All period

### Table of selected keys for previous date range
Key       | Description
--------- | -----------
previous | 	Previous
prev_last_week | Previous equal week
prev_last_month | Previous equal month
prev_last_quarter |	Previous equal quarter
prev_last_year | Previous equal year

### Table of dimension keys

Key       | Description
--------- | -----------
shop |	Stores
shop_group | Store Groups
shop_marker |	Shop Markers
category | Categories
product | Products
producer | Producers
brand | Brands 
product_marker | Product markers
assortment_type | Assortment types
supplier | Suppliers
bonus_type | 	Bonus Types
categorical_manager | Categorical managers
day_month_year | 	Day / Month / Year
month_year | 	Month / Year
quarter | Quarter
month | Month
week | Week
week_day | Day of the week
hour | Hours
terminal | Terminals
cashier | Cashiers
receipt_marker | Receipt Markers
promo | Promos
promo_type | Promo type
loyalty | Clients LP
loyalty_format | Types of PL clients
loyalty_group |	Clients groups LP
loss_type |	Types of Write-off (LossType)
price_type | 	Price type

# Table of reports and report widgets
Report slug     | Report      | Report widgets slug| Report widgets |
--------------- | ----------- | ------------------ | -------------- |
shops-sales     | Shop Sales  | table              | Report         |
                |             | treemap            | Sales Structure|
                |             |  treemap-stock     |                |
category-sales  | Category Sales | table           | Report         |
                |             | treemap            | Sales Structure|
                |             |  treemap-stock     |                |
brands-sales    | Brand Sales | table              | Report         |
                |             | treemap            | Sales Structure|
                |             |  treemap-stock     |                |
products-sales  |Product Sales| table              |Report          |
                |             |cumulative-curve    |Cumulative Curve|
                |             |abc                 | ABC            |
                |             |category-abc        |ABC by categories|
suppliers-analysis | Supplier Analysis | table     | Report         |
                |             | treemap            | Sales Structure|
                |             |  treemap-stock     |                |
producers-analysis | Producers Analysis | table    | Report         |
                |             | treemap            | Sales Structure|
                |             |  treemap-stock     |                |
markers-comparative |Markers Comparison | table    |                |
categorical-managers |Category managers | table    |                |
receipt-analysis |Receipts    | table              |                |
stock-control   |Stock Monitoring | table          |                |
out-of-stock    |Prediction of OoS | table         |                |
products-decommissioning |Unsaleable Products | table |             |
recalculation-products |Products for adjustment | table |           |
terminals-traffic |POS-terminal Traffic | table    |                |
staff-efficiency |Effectiveness of cashiers | table |               |
innovation-products |New SKU   | table             |                |
baskets-analysis |Basket Analysis | table          |2 dimensions    |
                |             |                    |3 dimensions    |
quadrant-analysis |Quadrant Analysis | table       |                |
movement-products |Products movement | document    |                |
predict-plan    | Sales Plans | table-by-shops     | Plan by shops  |
                |             |table-by-categories | Plan by categories |
                |             |table-by-categorical-managers | Plan by category managers|
promotion-analysis| Promotion Analysis | table     | Report         |
                  |                    | chart     | Promo Comparison |
effectiveness-loyalty-program|Effectiveness of Loyalty Program | table | Report  |
                  |                    | chart     | Dynamics of LP |
hourly-analysis   | 24-Hour Analysis   | chart     | Hour Trend       |
                  |                    | table     | 24-Hour Analysis |
                  |                    |click-detail-table|Hour Trend table after click|
rfm-analysis      | RFM - analysis     | chart     | Clients groups   |
                  |                    |loyalty-anomaly-table | Anomaly clients|
loyalty-statistics | Statistic of Loyalty Program | loyalty-dynamic |Dynamics of LP |
                   |                              | cumulative-curve|Cumulative curve |
                   |                              |outflow-clients  |Clients Churn |
                   |                              |sales-funnel     |Sales Funnel |
change-in-sales | Sales Change        | comparison-chart |           |
loyalty-segmentation| Customer Loyalty Segmentation | loyalty-clients-table  |Clients list  |
                    |                               | loyalty-purchases-table| Clients purchases |
comparative-dynamics| Comparative Dynamic | curr-chart |Selected period  | 
                    |                     | prev-chart |Previous period  |
                    |                     | diff-chart | Change in absolute units |
shop-card     | Shop Card | info   |                   |
              |           | cards  |                   |
              |           |chart   | Shop Dynamics     |                  
category-card | Category Card | price-interval-chart   | Products price distribution|
              |               |cards |
              |               |chart |Category Dynamics|
              |               |sku   |Category fullness|
product-card  | Product Card  | info |                   |
              |               |cards |                   |
              |               |chart | Product Dynamics  |
