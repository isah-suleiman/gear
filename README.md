# Data Portfolio: SQL, Excel, and Tableau

![Import-Data](assets/images/bike.png)

Table of Contents

# Objective

-   What is the key pain point

The company's management seeks to enhance their understanding of sales performance and identify trends within their sales data from 2016 to 2018. By delving into various dimensions of sales activity, they aim to extract actionable insights to inform strategic decisions

-   What is the ideal solution? To create a dashboard that provides insights into sales and profit for across all regions. including their
-   Revenue per Year
-   Revenue per Month
-   Total Units Sold
-   Number of Customers

This will help management know the most profitable stores over the past three years and how to improve the less profitable stores.

# Data Source

-   What data is needed to achieve our objective?

We need data on the sales performance including their:\
- Revenue per Store - Revenue per product - Revenue per Sales Rep - Top Customers

Where is the data coming from? The data is a sample dataset. I created a script with data and loaded it into MYSQL.

 [see here to find it.](https://www.kaggle.com/datasets/bhavyadhingra00020/top-100-social-media-influencers-2024-countrywise?resource=download)
# Stages

Design Development Analysis

## Design

Dashboard components required

-   What should the dashboard contain based on the requirements provided?

To understand what it should contain, we need to figure out what questions we need the dashboard to answer:

1.  What is the total revenue from 2016 to 2018?
2.  What is the revenue per month from 2016 to 2018?
3.  What is the revenue per state?
4.  What is the revenue per store?
5.  What are the most profitable brands?
6.  Who are the top customers?
7.  Who is the top-performing sales representative?

For now, these are some of the questions we need to answer, this may change as we progress down our analysis.

## Tools

| Tool       | Purpose                                               |
|------------|-------------------------------------------------------|
| Excel      | Exploring the data                                    |
| SQL Server | Cleaning, testing, and analyzing the data             |
| Tableau    | Visualizing the data via interactive dashboards       |
| GitHub     | Hosting the project documentation and version control |

# Development

## Pseudocode

-   What's the general approach in creating this solution from start to finish?

1.  Get the data
2.  Explore the data in Excel
3.  Load the data into SQL Server
4.  Clean the data with SQL
5.  Test the data with SQL
6.  Visualize the data in Tableau
7.  Generate the findings based on the insights
8.  Write the documentation + commentary
9.  Publish the data to GitHub Pages

# Data Creation

After downloading the dataset, I applied the following steps. 1. Opened SQL server, created a new database, and named it Bikestores 2. Select the new Bikestores database in the SQL server. 3. Open the 'create objects' query file and then execute it in the SQL server for the BikeStores database. 4. Opened the 'load data' query file and then executed it in the SQL server for the database.'

## Data exploration notes

This is the stage where you have a scan of what's in the data, errors, inconsistencies, bugs, weird and corrupted characters, etc

-   What are your initial observations with this dataset? What's caught your attention so far?

1.  The required fields are scattered around nine tables. so I would have to join the tables to extract the data.

2.  The primary key in one table is a foreign key in another so we can join them together.

### Transform the data

``` SQL
/*
# 1. Select id, customer name, city state order_date
# 2. We need the sales volume and the total revenue generated
# 3. Next, we add the name of the products 
# 4. Next, I'll add the category of the products that were purchased.
# 5. I'll then add the stores in which the sales took place
# 6. I'll then add the sales rep that made the sale
*/

-- 1.
SELECT
  ord.order_id,
  CONCAT(cus.first_name, ' ', cus.Last_name),
  cus.city,
  cus.state,
  ord.order_date
FROM sales.orders ord
JOIN sales.customers cus
ON ord.customer_id = cus.customer_id

-- 2.
SELECT
    ord.order_id,
    CONCAT(cus.first_name, ' ', cus.Last_name),
    cus.city,
    cus.state,
    ord.order_date,
    SUM(ite.quantity) AS 'Total_units',
    SUM(ite.quantity * ite.list_price) AS 'Revenue'
FROM sales.orders ord
JOIN sales.customers cus
ON ord.customer_id = cus.customer_id
JOIN sales.order_items ite
ON ord.order_id = ite.order_id
GROUP BY
    ord.order_id,
    CONCAT(cus.first_name, ' ', cus.Last_name),
    cus.city,
    cus.state,
    ord.order_date

-- 3.
SELECT
    ord.order_id,
    CONCAT(cus.first_name, ' ', cus.Last_name) AS 'Customer_Name',
    cus.city,
    cus.state,
    ord.order_date,
    SUM(ite.quantity) AS 'Total_units',
    SUM(ite.quantity * ite.list_price) AS 'Revenue',
    pro.product_name
FROM sales.orders ord
JOIN sales.customers cus
ON ord.customer_id = cus.customer_id
JOIN sales.order_items ite
ON ord.order_id = ite.order_id
JOIN production.products pro
ON ite.product_id = pro.product_id
GROUP BY
    ord.order_id,
    CONCAT(cus.first_name, ' ', cus.Last_name),
    cus.city,
    cus.state,
    ord.order_date,
    pro.product_name
    
-- 4.
SELECT
    ord.order_id,
    CONCAT(cus.first_name, ' ', cus.Last_name) AS 'Customer_Name',
    cus.city,
    cus.state,
    ord.order_date,
    SUM(ite.quantity) AS 'Total_units',
    SUM(ite.quantity * ite.list_price) AS 'Revenue',
    pro.product_name,
    cat.category_name
FROM sales.orders ord
JOIN sales.customers cus
ON ord.customer_id = cus.customer_id
JOIN sales.order_items ite
ON ord.order_id = ite.order_id
JOIN production.products pro
ON ite.product_id = pro.product_id
JOIN production.categories cat
ON pro.category_id = cat.category_id
GROUP BY
    ord.order_id,
    CONCAT(cus.first_name, ' ', cus.Last_name),
    cus.city,
    cus.state,
    ord.order_date,
    pro.product_name,
    cat.category_name
    
-- 5.
SELECT
    ord.order_id,
    CONCAT(cus.first_name, ' ', cus.Last_name) AS 'Customer_Name',
    cus.city,
    cus.state,
    ord.order_date,
    SUM(ite.quantity) AS 'Total_units',
    SUM(ite.quantity * ite.list_price) AS 'Revenue',
    pro.product_name,
    cat.category_name,
    sto.store_name
FROM sales.orders ord
JOIN sales.customers cus
ON ord.customer_id = cus.customer_id
JOIN sales.order_items ite
ON ord.order_id = ite.order_id
JOIN production.products pro
ON ite.product_id = pro.product_id
JOIN production.categories cat
ON pro.category_id = cat.category_id
JOIN sales.stores sto
ON ord.store_id = sto.store_id
GROUP BY
    ord.order_id,
    CONCAT(cus.first_name, ' ', cus.Last_name),
    cus.city,
    cus.state,
    ord.order_date,
    pro.product_name,
    cat.category_name,
    sto.store_name
    
-- 6.
SELECT
    ord.order_id,
    CONCAT(cus.first_name, ' ', cus.Last_name) AS 'Customer_Name',
    cus.city,
    cus.state,
    ord.order_date,
    SUM(ite.quantity) AS 'Total_units',
    SUM(ite.quantity * ite.list_price) AS 'Revenue',
    pro.product_name,
    cat.category_name,
    sto.store_name,
    CONCAT(sta.first_name, ' ', sta.last_name) AS 'Sales-rep',
    bra.brand_name
FROM sales.orders ord
JOIN sales.customers cus
ON ord.customer_id = cus.customer_id
JOIN sales.order_items ite
ON ord.order_id = ite.order_id
JOIN production.products pro
ON ite.product_id = pro.product_id
JOIN production.categories cat
ON pro.category_id = cat.category_id
JOIN sales.stores sto
ON ord.store_id = sto.store_id
JOIN sales.staffs sta
ON ord.staff_id = sta.staff_id
JOIN production.brands bra
ON pro.brand_id = bra.brand_id
GROUP BY
    ord.order_id,
    CONCAT(cus.first_name, ' ', cus.Last_name),
    cus.city,
    cus.state,
    ord.order_date,
    pro.product_name,
    cat.category_name,
    sto.store_name,
    CONCAT(sta.first_name, ' ', sta.last_name),
    bra.brand_name
```
###
Final Query
![Import-Data](assets/images/query.png)
#Import data from SQl to Microsoft Excel Create a Microsoft Excel file and connect it to the SQL server. Then I import the data into Excel 

![Import-Data](assets/images/Import_data_to_excel.png)

# Check if the data is in the right format.

All the data are in the correct format.

# Creating the Charts

1.  Next I'll create 8 different pivot charts and create visualizations from those charts.
2.  The charts include

-   Total Revenue
-   Revenue per Month
-   Revenue per State
-   Revenue per Store
-   Revenue Per Brand
-   Revenue per Product Category
-   Top 10 Customers
-   Best-performing Sales Representatives

### Total Revenue & Total Revenue Per Month
![Import-Data](assets/images/Total_year_month.png)

### Revenue for Each State and Store
![Import-Data](assets/images/Revenue_Store_State.png)

### Revenue for Each Brand and Product Category
![Import-Data](assets/images/Revenue_Brand_Category.png)

### Top 10 Customers & Top Sales Representatives
![Import-Data](assets/images/Top_Customers_Rep.png)

3.  Create a Dashboard that includes slicers for management


# Import the Data Into Tableau to Create an Interactive Dashboard

1. Import the Excel version into Tableau
2. Confirm the data structure
3. Create a new worksheet for Revenue per Year
![Import-Data](assets/images/Tab_Rev_Year.png)
4. Revenue per Month
![Import-Data](assets/images/Tab_Rev_Month.png)
5. Revenue Per State
![Import-Data](assets/images/Tab_Rev_State.png)
6. Revenue Per Store
![Import-Data](assets/images/Tab_Rev_Store.png)
7. Revenue per Brand
![Import-Data](assets/images/Tab_Rev_Brand.png)
8. Revenue per Category
![Import-Data](assets/images/Tab_Rev_Category.png)
9. Top 10 Customers
![Import-Data](assets/images/Tab_Top_Customers.png)
10. Top Sales Rep
![Import-Data](assets/images/Tab_Sales_Rep.png)
