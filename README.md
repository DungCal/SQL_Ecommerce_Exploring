# SQL_Ecommerce_Exploring

## Table of Contents:
1. [Introduction and Motivation](#c1)
2. [The goal of creating this project](#c2)
3. [How to access and import data](#c3)
4. [Read and explain dataset](#c4)
5. [Data Processing & Exploratory Data Analysis](#c5)
6. [Ask questions and solve it](#c6)
7. [Conclusion](#c7)

<div id='c1'/>

## 1. Introduction and Motivation

The sample dataset contains obfuscated Google Analytics 360 data from the Google Merchandise Store, a real ecommerce store. The Google Merchandise Store sells Google branded merchandise. The data is typical of what you would see for an ecommerce website. It includes the following kinds of information:
- Traffic source data: information about where website visitors originate. This includes data about organic traffic, paid search traffic, display traffic, etc.
- Content data: information about the behavior of users on the site. This includes the URLs of pages that visitors look at, how they interact with content, etc.
- Transactional data: information about the transactions that occur on the Google Merchandise Store website.

Based on the dataset, we perform queries to analyze business activities in 2017, include calculating total bounces, hits and pageviews, analyzing the behaviours of customers, and other analysis of porducts. This project aims to get information of business situation, improve marketing efficiency and analysing products.

<div id='c2'/>

## 2. The particular goal of the project

This project will make some analysis including:
- Overview of business data
- Bouce rate Analysis
- Revenue Analysis
- Transaction Analysis
- Product Analysis

<div id='c3'/>
## 3. How to access and import data

- Go to https://console.cloud.google.com/bigquery
- Create a project
- Once you have a project, use [this link](https://console.cloud.google.com/marketplace/product/obfuscated-ga360-data/obfuscated-ga360-data?project=lexical-script-761) to access the dataset. Click **View Dataset** to open the dataset in your project.
- Click **Query Table** to run a query.

<div id='c4'/>

## 4. Read and explain dataset

https://support.google.com/analytics/answer/3437719?hl=en&ref_topic=3416089&sjid=12699874752430297142-AP

| Field Name | Data Type | Description |
| --- | --- | ---|
| fullVisitorId | STRING | The unique visitor ID. |
|date	| STRING | The date of the session in YYYYMMDD format. |
| totals | RECORD |	This section contains aggregate values across the session. |
| totals.bounces | INTEGER | Total bounces (for convenience). For a bounced session, the value is 1, otherwise it is null. |
| totals.hits |	INTEGER	| Total number of hits within the session.|
totals.pageviews | INTEGER | Total number of pageviews within the session. |
| totals.visits	| INTEGER	| The number of sessions (for convenience). This value is 1 for sessions with interaction events. The value is null if there are no interaction events in the session. |
| totals.transactions |	INTEGER	| Total number of ecommerce transactions within the session. |
| trafficSource.source | STRING	| The source of the traffic source. Could be the name of the search engine, the referring hostname, or a value of the utm_source URL parameter. |
| hits | RECORD	| This row and nested fields are populated for any and all types of hits. |
| hits.eCommerceAction | RECORD	| This section contains all of the ecommerce hits that occurred during the session. This is a repeated field and has an entry for each hit that was collected. |
| hits.eCommerceAction.action_type | STRING	| The action type. Click through of product lists = 1, Product detail views = 2, Add product(s) to cart = 3, Remove product(s) from cart = 4, Check out = 5, Completed purchase = 6, Refund of purchase = 7, Checkout options = 8, Unknown = 0. Usually this action type applies to all the products in a hit, with the following exception: when hits.product.isImpression = TRUE, the corresponding product is a product impression that is seen while the product action is taking place (i.e., a ""product in list view"").<br>Example query to calculate number of products in list views:<br>SELECT<br>COUNT(hits.product.v2ProductName)<br>FROM [foo-160803:123456789.ga_sessions_20170101]<br>WHERE hits.product.isImpression == TRUE<br>Example query to calculate number of products in detailed view:<br>SELECT<br>COUNT(hits.product.v2ProductName),<br>FROM<br>[foo-160803:123456789.ga_sessions_20170101]<br>WHERE<br>hits.ecommerceaction.action_type = '2'<br>AND ( BOOLEAN(hits.product.isImpression) IS NULL OR BOOLEAN(hits.product.isImpression) == FALSE )" |
| hits.product | RECORD | This row and nested fields will be populated for each hit that contains Enhanced Ecommerce PRODUCT data. |
| hits.product.productQuantity | INTEGER | The quantity of the product purchased. |
| hits.product.productRevenue	| INTEGER	| The revenue of the product, expressed as the value passed to Analytics multiplied by 10^6 (e.g., 2.40 would be given as 2400000). |
| hits.product.productSKU	| STRING | Product SKU. |
| hits.product.v2ProductName | STRING |	Product Name. |
| fullVisitorId	| STRING | The unique visitor ID. |

<div id='c5'/>

## Explore Data Analysis

<div id='c6'/>

## 6. Ask questions and solve it

**Query 01: calculate total visit, pageview, transaction for Jan, Feb and March 2017 (order by month)**

~~~sql
with
  jan_feb_mar as(--3 month data
    select *
      ,(case when _table_suffix between '0101' and '0131' then '201701'
             when _table_suffix between '0201' and '0231' then '201702'
             when _table_suffix between '0301' and '0331' then '201703'
      end) as new_month
    FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
    where _table_suffix between '0101' and '0331')

select --total fact
  new_month as month
  ,sum(totals.visits) as visits
  ,sum(totals.pageviews) as pageviews
  ,sum(totals.transactions) as transactions 
from jan_feb_mar
group by new_month
order by month;
~~~~


