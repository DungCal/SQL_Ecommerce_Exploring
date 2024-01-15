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
   SELECT 
    FORMAT_DATE("%Y%m",PARSE_DATE("%Y%m%d",date)) as month_extract
    ,SUM(totals.visits) as visits
    ,SUM(totals.pageviews) as pageviews
    ,SUM(totals.transactions) as transactions
    ,ROUND(SUM(totals.totalTransactionRevenue)/POW(10,6),2) revenue
   FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
   WHERE _table_suffix BETWEEN '0101' AND '0331'
   GROUP BY month_extract
~~~~

| month_extract | visits | pageviews | transactions | revenue |
| --- | --- | --- | --- | --- |
| 201701 | 64694 | 257708 | 713 | 106248.15 |
| 201702 | 62192 | 233373 | 733 | 116111.6 |
| 201703 | 69931 | 259522 | 993 | 150224.7 |

The table above provided the overview of the business performance of Ecommerce Store website in some first few months of 2017. Some performances include number of visits, pageviews, transactions and amounts of revenue. Throughout the data, there are insights that can be devrived: 

**Traffic and Engagement Patterns**: the numbers of visits and pageviews increase from 201701 to 201703, although there is a slight decrease in 201702. This may be the influence of seasonality or marketing performance. However, the interests of people in website increase over time. People explore multiple pages during their visits, and more and more people visits our website.

** Transactions and Revenue trends**: Number of transactions shows a sharp increase from 201701 to 201703. People are engaging more with the website, there is a growing proportion of them making transactions, contributing to increased revenue. The store has taken actions to improve conversion rate, this may be a reason for increases in transactions and revenues.

**Seasonal or Marketing Influence**:The consistent growth in both transactions and revenue over the three months could indicate the influence of seasonality, marketing campaigns, or optimizations implemented during this time.

**Opportunities for Improvement**: While the data shows positive growth, there might be opportunities to optimize user engagement and conversions further. Analyzing specific user journeys, popular landing pages, and exit points could help identify areas for improvement.

**Future Strategy and Focus**: Consider further investigating what strategies, campaigns, or changes were implemented between January and March that contributed to the significant increase in transactions and revenue. These insights can inform future marketing and optimization efforts.

## 6.2 Bounce rate per traffic source in July 2017

~~~~sql
SELECT trafficSource.source
       ,COUNT(visitNumber) total_visits
       ,SUM(totals.bounces) total_no_of_bounces
       ,ROUND((SUM(totals.bounces)/COUNT(visitNumber))*100,2) bounce_rate
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`
GROUP BY trafficSource.source
ORDER BY total_visits DESC;
~~~~

| source                      | total_visits | total_no_of_bounces | bounce_rate |
|-----------------------------|--------------|---------------------|-------------|
| google                      | 38400        | 19798               | 51.56       |
| (direct)                    | 19891        | 8606                | 43.27       |
| youtube.com                 | 6351         | 4238                | 66.73       |
| analytics.google.com        | 1972         | 1064                | 53.96       |
| Partners                    | 1788         | 936                 | 52.35       |
| m.facebook.com              | 669          | 430                 | 64.28       |
| google.com                  | 368          | 183                 | 49.73       |
| dfa                         | 302          | 124                 | 41.06       |
| sites.google.com            | 230          | 97                  | 42.17       |
| facebook.com                | 191          | 102                 | 53.4        |
| reddit.com                  | 189          | 54                  | 28.57       |
| qiita.com                   | 146          | 72                  | 49.32       |
| baidu                       | 140          | 84                  | 60          |
| quora.com                   | 140          | 70                  | 50          |
| bing                        | 111          | 54                  | 48.65       |
| mail.google.com             | 101          | 25                  | 24.75       |
| yahoo                       | 100          | 41                  | 41          |
| blog.golang.org             | 65           | 19                  | 29.23       |
| l.facebook.com              | 51           | 45                  | 88.24       |
| groups.google.com           | 50           | 22                  | 44          |
| t.co                        | 38           | 27                  | 71.05       |
| google.co.jp                | 36           | 25                  | 69.44       |
| m.youtube.com               | 34           | 22                  | 64.71       |
| dealspotr.com               | 26           | 12                  | 46.15       |
| productforums.google.com    | 25           | 21                  | 84          |
| ask                         | 24           | 16                  | 66.67       |
| support.google.com          | 24           | 16                  | 66.67       |
| int.search.tb.ask.com       | 23           | 17                  | 73.91       |
| optimize.google.com         | 21           | 10                  | 47.62       |
| docs.google.com             | 20           | 8                   | 40          |
| lm.facebook.com             | 18           | 9                   | 50          |
| l.messenger.com             | 17           | 6                   | 35.29       |
| adwords.google.com          | 16           | 7                   | 43.75       |
| duckduckgo.com              | 16           | 14                  | 87.5        |
| google.co.uk                | 15           | 7                   | 46.67       |
| sashihara.jp                | 14           | 8                   | 57.14       |
| lunametrics.com             | 13           | 8                   | 61.54       |
| search.mysearch.com         | 12           | 11                  | 91.67       |
| tw.search.yahoo.com         | 10           | 8                   | 80          |
| outlook.live.com            | 10           | 7                   | 70          |
| phandroid.com               | 9            | 7                   | 77.78       |
| connect.googleforwork.com   | 8            | 5                   | 62.5        |
| plus.google.com             | 8            | 2                   | 25          |
| m.yz.sm.cn                  | 7            | 5                   | 71.43       |
| google.co.in                | 6            | 3                   | 50          |
| search.xfinity.com          | 6            | 6                   | 100         |
| google.ru                   | 5            | 1                   | 20          |
| online-metrics.com          | 5            | 2                   | 40          |
| hangouts.google.com         | 5            | 1                   | 20          |
| s0.2mdn.net                 | 5            | 3                   | 60          |
| m.sogou.com                 | 4            | 3                   | 75          |
| in.search.yahoo.com         | 4            | 2                   | 50          |
| googleads.g.doubleclick.net | 4            | 1                   | 25          |
| away.vk.com                 | 4            | 3                   | 75          |
| getpocket.com               | 3            |                     |             |
| m.baidu.com                 | 3            | 2                   | 66.67       |
| siliconvalley.about.com     | 3            | 2                   | 66.67       |
| msn.com                     | 2            | 1                   | 50          |
| google.it                   | 2            | 1                   | 50          |
| google.co.th                | 2            | 1                   | 50          |
| wap.sogou.com               | 2            | 2                   | 100         |
| calendar.google.com         | 2            | 1                   | 50          |
| github.com                  | 2            | 2                   | 100         |
| plus.url.google.com         | 2            |                     |             |
| myactivity.google.com       | 2            | 1                   | 50          |
| centrum.cz                  | 2            | 2                   | 100         |
| search.1and1.com            | 2            | 2                   | 100         |
| uk.search.yahoo.com         | 2            | 1                   | 50          |
| au.search.yahoo.com         | 2            | 2                   | 100         |
| m.sp.sm.cn                  | 2            | 2                   | 100         |
| google.cl                   | 2            | 1                   | 50          |
| moodle.aurora.edu           | 2            | 2                   | 100         |
| amp.reddit.com              | 2            | 1                   | 50          |
| newclasses.nyu.edu          | 1            |                     |             |
| google.es                   | 1            | 1                   | 100         |
| google.ca                   | 1            |                     |             |
| malaysia.search.yahoo.com   | 1            | 1                   | 100         |
| kidrex.org                  | 1            | 1                   | 100         |
| gophergala.com              | 1            | 1                   | 100         |
| aol                         | 1            |                     |             |
| google.nl                   | 1            |                     |             |
| kik.com                     | 1            | 1                   | 100         |
| earth.google.com            | 1            |                     |             |
| ph.search.yahoo.com         | 1            |                     |             |
| web.mail.comcast.net        | 1            | 1                   | 100         |
| google.bg                   | 1            | 1                   | 100         |
| news.ycombinator.com        | 1            | 1                   | 100         |
| es.search.yahoo.com         | 1            | 1                   | 100         |
| it.pinterest.com            | 1            | 1                   | 100         |
| mx.search.yahoo.com         | 1            | 1                   | 100         |
| images.google.com.au        | 1            | 1                   | 100         |
| search.tb.ask.com           | 1            |                     |             |
| arstechnica.com             | 1            |                     |             |
| web.facebook.com            | 1            | 1                   | 100         |
| online.fullsail.edu         | 1            | 1                   | 100         |
| google.com.br               | 1            |                     |             |
| suche.t-online.de           | 1            | 1                   | 100         |

The table shows some traffic website and their key metrics, which help analyze user behaviours and engagement. These metrics include: source, total_visits, total_no_of_bounces, bounce_rates. Bounce Rate is defined as the percentage of visitors that leave a webpage without taking an action, such as clicking on a link, filling out a form, or making a purchase. Because a lower bounce rate generally indicates that users are more engaged with the website content, while a higher bounce rate may suggest that users are leaving the site after viewing only a single page. 
