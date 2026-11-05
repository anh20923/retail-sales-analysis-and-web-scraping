# Retail Sales Analysis and Web Scraping

This project uses MySQL to analyse orders in a vehicle store and Python to collect skincare product information and customer reviews from Hasaki. The two parts use separate datasets. Start with the [SQL notebook](part_1_sql_queries.ipynb) to explore the sales analysis, or open the [product CSV](products.csv) to see the web scraping results.

## Vehicle store sales analysis

The SQL notebook answers 20 questions about orders, customers, employees and payments in the `vehiclestore` database. It uses joins to connect orders to customers and order lines, aggregates to calculate order values, and subqueries to identify customers who never placed an order. Two Matplotlib charts show annual order counts and the ten customers who placed the most orders.

The saved results show **6 cancelled orders**. Annual order counts were **111 in 2003**, **151 in 2004** and **64 in 2005**. Euro+ Shopping Channel placed the most orders (**26**), followed by Mini Gifts Distributors Ltd. (**17**).

## Hasaki product and review data

The [product notebook](part_2a_product_data_collection.ipynb) used Selenium to collect links from 13 catalogue pages and extract details for **520 skincare products**. The [review notebook](part_2b_customer_reviews.ipynb) visited those product pages to collect review details. Both notebooks saved intermediate records as JSON and used pandas to export CSV files.

| File | Contents |
| --- | --- |
| [products.csv](products.csv) | 520 products with product codes, names, sale and original prices, variants, descriptions and average ratings. |
| [customer_reviews.csv](customer_reviews.csv) | 520 product-level records with fields for reviewer name, review text, date, time and star rating. The notebook writes one record per product, even when a page has multiple reviews. |
| [facial_skin_care_product_links.txt](facial_skin_care_product_links.txt) | Product page URLs used by the collection notebooks. |
| [Product JSON](data/facial_skin_care_products/) and [review JSON](data/customer_reviews/) | Intermediate records used to build the two CSV files. |
| [mylibs.py](mylibs.py) | Helper functions for reading and writing local files. |

Both CSVs use Vietnamese column names. Join them on `Ma_san_pham` (product code). For example, this displays product names alongside the star ratings recorded in the review export:

```python
import pandas as pd

products = pd.read_csv("products.csv", dtype={"Ma_san_pham": str})
reviews = pd.read_csv("customer_reviews.csv", dtype={"Ma_san_pham": str})
rated_products = products.merge(reviews, on="Ma_san_pham", how="left")
print(rated_products[["Ten_san_pham", "So_sao"]].head())
```

## Running the notebooks

The included CSVs and saved notebook outputs can be explored offline. To run the notebooks locally, open a terminal in the project directory and install the Python packages:

```bash
python -m pip install notebook pandas matplotlib seaborn selenium requests ipython-sql pymysql mysql-connector-python
python -m notebook
```

- **SQL analysis:** Open [part_1_sql_queries.ipynb](part_1_sql_queries.ipynb). To rerun the queries, configure its connection cell for a MySQL instance containing the `vehiclestore` database, then run the cells in order.
- **Web scraping:** Install Google Chrome and ensure Selenium can launch it. Run the [product notebook](part_2a_product_data_collection.ipynb) and then the [review notebook](part_2b_customer_reviews.ipynb) from the project directory. A live run writes new JSON and CSV output. Product listings, reviews and page layouts on Hasaki may have changed since the saved collection.
