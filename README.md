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

The table shows some traffic website and their key metrics, which help analyze user behaviours and engagement. These metrics include: source, total_visits, total_no_of_bounces, bounce_rates. Bounce Rate is defined as the percentage of visitors that leave a webpage without taking an action, such as clicking on a link, filling out a form, or making a purchase. Because a lower bounce rate generally indicates that users are more engaged with the website content, while a higher bounce rate may suggest that users are leaving the site after viewing only a single page. Throughout this data, several big website have large number of visits and number of bounces, however their bounces vary from 40% to 60%(google.com has 51.56%, direct link is 43%, youtube is a bit higher, 66%). This may indicate that users are more engaged with the content of these websites. There are some traffic websites which has high bounces rates: l.facebook.com with 88%, productforums.google.com with 84% and many websites have bounce rate almost 100% due to their lack of visits. It's important to note that while addressing high bounce rates is essential, the context of the source and user behavior should be thoroughly analyzed to determine the most effective strategies for improvement. 

** 6.3 Revenue by traffic source by week, by month in June 2017**

~~~sql

with 
month_data as(
  SELECT
    "Month" as time_type,
    format_date("%Y%m", parse_date("%Y%m%d", date)) as month,
    trafficSource.source AS source,
    SUM(p.productRevenue)/1000000 AS revenue
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201706*`,
    unnest(hits) hits,
    unnest(product) p
  WHERE p.productRevenue is not null
  GROUP BY 1,2,3
  order by revenue DESC
),

week_data as(
  SELECT
    "Week" as time_type,
    format_date("%Y%W", parse_date("%Y%m%d", date)) as date,
    trafficSource.source AS source,
    SUM(p.productRevenue)/1000000 AS revenue
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201706*`,
    unnest(hits) hits,
    unnest(product) p
  WHERE p.productRevenue is not null
  GROUP BY 1,2,3
  order by revenue DESC
)

select * from month_data
union all
select * from week_data
order by revenue desc

~~~~

| time_type | time   | source                        | revenue  |
|-----------|--------|-------------------------------|----------|
| Month     | 201706 | (direct)                      | 97231.62 |
| WEEK      | 201724 | (direct)                      | 30883.91 |
| WEEK      | 201725 | (direct)                      | 27254.32 |
| Month     | 201706 | google                        | 18757.18 |
| WEEK      | 201723 | (direct)                      | 17302.68 |
| WEEK      | 201726 | (direct)                      | 14905.81 |
| WEEK      | 201724 | google                        | 9217.17  |
| Month     | 201706 | dfa                           | 8841.23  |
| WEEK      | 201722 | (direct)                      | 6884.9   |
| WEEK      | 201726 | google                        | 5330.57  |
| WEEK      | 201726 | dfa                           | 3704.74  |
| Month     | 201706 | mail.google.com               | 2563.13  |
| WEEK      | 201724 | mail.google.com               | 2486.86  |
| WEEK      | 201724 | dfa                           | 2341.56  |
| WEEK      | 201722 | google                        | 2119.39  |
| WEEK      | 201722 | dfa                           | 1670.65  |
| WEEK      | 201723 | dfa                           | 1124.28  |
| WEEK      | 201723 | google                        | 1083.95  |
| WEEK      | 201725 | google                        | 1006.1   |
| WEEK      | 201723 | search.myway.com              | 105.94   |
| Month     | 201706 | search.myway.com              | 105.94   |
| Month     | 201706 | groups.google.com             | 101.96   |
| WEEK      | 201725 | mail.google.com               | 76.27    |
| Month     | 201706 | chat.google.com               | 74.03    |
| WEEK      | 201723 | chat.google.com               | 74.03    |
| WEEK      | 201724 | dealspotr.com                 | 72.95    |
| Month     | 201706 | dealspotr.com                 | 72.95    |
| WEEK      | 201725 | mail.aol.com                  | 64.85    |
| Month     | 201706 | mail.aol.com                  | 64.85    |
| WEEK      | 201726 | groups.google.com             | 63.37    |
| Month     | 201706 | phandroid.com                 | 52.95    |
| WEEK      | 201725 | phandroid.com                 | 52.95    |
| Month     | 201706 | sites.google.com              | 39.17    |
| WEEK      | 201725 | groups.google.com             | 38.59    |
| WEEK      | 201725 | sites.google.com              | 25.19    |
| Month     | 201706 | google.com                    | 23.99    |
| WEEK      | 201725 | google.com                    | 23.99    |
| Month     | 201706 | yahoo                         | 20.39    |
| WEEK      | 201726 | yahoo                         | 20.39    |
| WEEK      | 201723 | youtube.com                   | 16.99    |
| Month     | 201706 | youtube.com                   | 16.99    |
| WEEK      | 201724 | bing                          | 13.98    |
| Month     | 201706 | bing                          | 13.98    |
| WEEK      | 201722 | sites.google.com              | 13.98    |
| WEEK      | 201724 | l.facebook.com                | 12.48    |
| Month     | 201706 | l.facebook.com                | 12.48    |

This table shows data of revenues generated from different sources during June 2017. The rows include time_type, time, source and revenue. 

**Web Source Breakdown**: The dataset includes revenue from different sites such as direct site, google, dfa, mail, and facebook. Websites come from many different kind of sources, include direct web, search traffic, social website and workspace website,...
**Time Analysis**: Revenue is reported on a weekly and monthly basis. In this data, revenue data is taken from June 2017 and its weeks of the year 2017.

** 6.4: Average number of pageviews by purchaser type (purchasers vs non-purchasers) in June, July 2017 **

~~~~sql

with 
purchaser_data as(
  select
      format_date("%Y%m",parse_date("%Y%m%d",date)) as month,
      (sum(totals.pageviews)/count(distinct fullvisitorid)) as avg_pageviews_purchase,
  from `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
    ,unnest(hits) hits
    ,unnest(product) product
  where _table_suffix between '0601' and '0731'
  and totals.transactions>=1
  and product.productRevenue is not null
  group by month
),

non_purchaser_data as(
  select
      format_date("%Y%m",parse_date("%Y%m%d",date)) as month,
      sum(totals.pageviews)/count(distinct fullvisitorid) as avg_pageviews_non_purchase,
  from `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
      ,unnest(hits) hits
    ,unnest(product) product
  where _table_suffix between '0601' and '0731'
  and totals.transactions is null
  and product.productRevenue is null
  group by month
)

select
    pd.*,
    avg_pageviews_non_purchase
from purchaser_data pd
left join non_purchaser_data using(month)
order by pd.month;

~~~~

| month  | avg_pageviews_purchase | avg_pageviews_non_purchase |
|--------|------------------------|----------------------------|
| 201706 | 94.02          | 316.87               |
| 201707 | 124.24            | 334.06               |

 **Pageviews for Users Making a Purchase vs. Non-Purchasers:** The data highlights a significant difference in average pageviews between users who made a purchase and those who did not. On average, users who made a purchase tend to view much les pages on the website than those who did not. In June 2017, for instance, the average pageviews for purchasers (25.74) were much higher compared to non-purchasers (4.07).

 **Engagement Patterns:** Users who open more webpages are likely not to engage with the website content, therefore they are not likely to make a purchase. The reason may be the users cannot find the appropriate context of the website, or they cannot find the product they want on the website. There might be a negative correlation between engagement and conversion.

 **Behavioural insights:** The ones who makes purchase spend less time reading content in the store than the ones who do not. Although 94 pages is not a small number but these users decided to purchase without using much time to explore the products, read reviews and compare between products. On the contrary, users who spend much time on pages tends not to order. This may indicate that these users have no intention of making purchases and merely browse without interacting. Moreover, the website does not contain the content or products they want, they have to surf for a while. 

 **Conversion Optimization:** The business can leverage this data to optimize its website's design and content to turn conversion rate into revenues. This could involve extracting the needs of users, encouraging them to buy, order more discounts, or recommending the mosst suitable products for users spend much time in pages,...

 **6.5 Average number of transactions per user that made a purchase in July 2017**

 ~~~~sql
select
    format_date("%Y%m",parse_date("%Y%m%d",date)) as month,
    sum(totals.transactions)/count(distinct fullvisitorid) as Avg_total_transactions_per_user
from `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`
    ,unnest (hits) hits,
    unnest(product) product
where  totals.transactions>=1
and totals.totalTransactionRevenue is not null
and product.productRevenue is not null
group by month;
~~~~
 
| Month | Avg_total_transactions_per_user |
| --- | --- |
| 201707 | 4.163900415 |

This table shows the average number of transactions which an user making purchases made in July 2017. This number can be used to understand user behavior, track user engagement with your platform, or evaluate the effectiveness of marketing campaigns or promotions during that specific month.

**6.6 Average amount of money spent per session. Only include purchaser data in July 2017**

~~~~sql
select
    format_date("%Y%m",parse_date("%Y%m%d",date)) as month,
    ((sum(product.productRevenue)/sum(totals.visits))/power(10,6)) as avg_revenue_by_user_per_visit
from `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`
  ,unnest(hits) hits
  ,unnest(product) product
where product.productRevenue is not null
group by month;
~~~~

| Month | avg_revenue_by_user_per_visit |
| --- | --- |
| 201707 | 43.85 |

43.85 dollars is the average revenue by user per visit on website in July 2017. This suggests that an user spend 43.85 dollars for average per visit during July 2017. This could be an important metric to measure the marketing campaigns and the ability of a business to maximize the value from each transaction.

**6.7 Other products purchased by customers who purchased product "YouTube Men's Vintage Henley" in July 2017**

~~~~sql
with buyer_list as(
    SELECT
        distinct fullVisitorId
    FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`
    , UNNEST(hits) AS hits
    , UNNEST(hits.product) as product
    WHERE product.v2ProductName = "YouTube Men's Vintage Henley"
    AND totals.transactions>=1
    AND product.productRevenue is not null
)

SELECT
  product.v2ProductName AS other_purchased_products,
  SUM(product.productQuantity) AS quantity
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`
, UNNEST(hits) AS hits
, UNNEST(hits.product) as product
JOIN buyer_list using(fullVisitorId)
WHERE product.v2ProductName != "YouTube Men's Vintage Henley"
 and product.productRevenue is not null
GROUP BY other_purchased_products
ORDER BY quantity DESC;
~~~~


| other_purchased_products                                 | quantity |
|----------------------------------------------------------|----------|
| Google Sunglasses                                        | 20       |
| Google Womens Vintage Hero Tee Black                     | 7        |
| SPF-15 Slim & Slender Lip Balm                           | 6        |
| Google Womens Short Sleeve Hero Tee Red Heather          | 4        |
| YouTube Mens Fleece Hoodie Black                         | 3        |
| Google Mens Short Sleeve Badge Tee Charcoal              | 3        |      
| YouTube Twill Cap                                        | 2        |
| Red Shine 15 oz Mug                                      | 2        |
| Google Doodle Decal                                      | 2        |
| Recycled Mouse Pad                                       | 2        |
| Google Mens Short Sleeve Hero Tee Charcoal               | 2        |
| Android Womens Fleece Hoodie                             | 2        |
| 22 oz YouTube Bottle Infuser                             | 2        |
| Android Mens Vintage Henley                              | 2        |
| Crunch Noise Dog Toy	                                   | 2        |
| Android Wool Heather Cap Heather/Black                   | 2        |
| Google Mens Vintage Badge Tee Black                      | 1        |
| Google Twill Cap                                         | 1        |
| Google Mens Long & Lean Tee Grey                         | 1        |
| Google Mens Long & Lean Tee Charcoal                     | 1        |
| Google Laptop and Cell Phone Stickers                    | 1        |
| Google Mens Bike Short Sleeve Tee Charcoal               | 1        |
| Google 5-Panel Cap                                       | 1        |
| Google Toddler Short Sleeve T-shirt Grey                 | 1        |
| Android Sticker Sheet Ultra Removable                    | 1        |
| YouTube Custom Decals                                    | 1        |
| Four Color Retractable Pen                               | 1        |
| Google Mens Long Sleeve Raglan Ocean Blue                | 1        |
| Google Mens Vintage Badge Tee White                      | 1        |
| Google Mens 100% Cotton Short Sleeve Hero Tee Red        | 1        |
| Android Mens Vintage Tank                                | 1        |
| Google Mens Performance Full Zip Jacket Black            | 1        |
| 26 oz Double Wall Insulated Bottle                       | 1        |
| Google Mens  Zip Hoodie                                  | 1        |
| YouTube Womens Short Sleeve Hero Tee Charcoal            | 1        |
| Google Mens Pullover Hoodie Grey                         | 1        |
| YouTube Mens Short Sleeve Hero Tee White                 | 1        |
| Android Mens Short Sleeve Hero Tee White                 | 1        |
| Android Mens Pep Rally Short Sleeve Tee Navy             | 1        |
| YouTube Mens Short Sleeve Hero Tee Black                 | 1        |
| Google Slim Utility Travel Bag                           | 1        |
| Android BTTF Moonshot Graphic Tee                        | 1        |
| Google Mens Airflow 1/4 Zip Pullover Black               | 1        |
| Google Womens Long Sleeve Tee Lavender                   | 1        |
| 8 pc Android Sticker Sheet                               | 1        |
| YouTube Hard Cover Journal                               | 1        |
| Android Mens Short Sleeve Hero Tee Heather               | 1        |
| YouTube Womens Short Sleeve Tri-blend Badge Tee Charcoal | 1        |
| Google Mens Performance 1/4 Zip Pullover Heather/Black   | 1        |
| YouTube Mens Long & Lean Tee Charcoal                    | 1        |

The data provides the name and quantities sold of poroducts, which are purchased by customers who purchase "YouTube Men's Vintage Henley" during July 2017. Overall, the data provides valuable insights into customer preferences, product popularity, and potential areas for marketing and merchandising strategies. Further analysis of historical data and integration with customer demographics could provide a more comprehensive understanding of these trends.

**6.8 Calculate cohort map from product view to add_to_cart/number_product_view.**

~~~sql
with product_data as(
select
    format_date('%Y%m', parse_date('%Y%m%d',date)) as month,
    count(CASE WHEN eCommerceAction.action_type = '2' THEN product.v2ProductName END) as num_product_view,
    count(CASE WHEN eCommerceAction.action_type = '3' THEN product.v2ProductName END) as num_add_to_cart,
    count(CASE WHEN eCommerceAction.action_type = '6' and product.productRevenue is not null THEN product.v2ProductName END) as num_purchase
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_*`
,UNNEST(hits) as hits
,UNNEST (hits.product) as product
where _table_suffix between '20170101' and '20170331'
and eCommerceAction.action_type in ('2','3','6')
group by month
order by month
)

select
    *,
    round(num_add_to_cart/num_product_view * 100, 2) as add_to_cart_rate,
    round(num_purchase/num_product_view * 100, 2) as purchase_rate
from product_data;

~~~~

| month | num_product_view | num_add_to_cart | num_purchase | add_to_cart_rate | purchase_rate |
| --- | --- | --- | --- | --- | --- |
| 201701 | 25787 | 7342 | 2143 | 28.47 | 8.31 |
| 201702 | 21489 | 7360 | 2060 | 34.25 | 9.59 |
| 201703 | 23549 | 8782 | 2977 | 37.29 | 12.64 |

The table shows 5 metrics and rates of views, add-to-cart and purchases in three first months of 2017. The overall trend is increase, but for number of products views. This may suggests that the need of our customers is decreasing. 

More details, from Feb to March 2017, nunmber of add-to-cart products and number of purchases show sharp inceases. Therefore, add-to-cart rate and purchase rate notably increase in March 2017. This suggests that there is improvement in user experience or marketing campaigns.

<div id='c7'/>
  
## 7. Conclusion

- This is the author's opportunity to learn about the marketing industry and the customer journey through this e-commerce dataset.

- In analyzing the e-commerce dataset using BigQuery, the author understands customer behavior through the bounce rate, transaction, revenue, visit, and purchase.
  
- The author gained insights into which marketing channels drive traffic and sales by examining referral sources. Investing resources in effective channels and optimizing underperforming ones can improve marketing ROI.

- In conclusion, exploring the e-commerce dataset on BigQuery unearthed a wealth of insights critical for strategic decision-making to help the business can optimize operations, enhance customer experiences, and drive revenue.
