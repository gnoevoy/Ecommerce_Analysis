# Website Performance and Traffic Sources Analysis

This Power BI report presents a detailed analysis to highlights key metrics, trends, and insights essential for understanding the website's effectiveness and the impact of different traffic sources on overall performance.

</br>

### Annual Overview & Traffic Source Insights
This report page displays key yearly metrics including total sessions, orders, conversion rate, and profit, alongside a monthly trend analysis of growth. It also emphasizes the most profitable traffic source.

![website_performance_and_traffic_sources_page-0001](https://github.com/gnoevoy/Ecommerce_and_Web_Analytics/assets/43414592/e0d9da39-66d6-4ada-8f48-f174fc54a84e)

> **Note**
> **Conversion rate** – a measure that shows of how many people make an order, compared to the total number of visitors.
> </br>
> **Conversion Rate** = (Total Orders / Total Sessions) * 100

```sql
# Task 1
# Calculate the following metrics for each traffic source: total number of sessions, total number of orders, conversion rate, profit.
# Perform a monthly analysis to identify growth trends based on the previous metrics.

# Key metrics

SELECT COALESCE(utm_source, 'direct') AS utm_source,
    COUNT(s.website_session_id) AS sessions,
    COUNT(order_id) AS orders,
    COUNT(order_id) / COUNT(s.website_session_id) AS conversion_rate,
    SUM(price_usd - cogs_usd) AS profit
FROM website_sessions AS s
LEFT JOIN orders AS o 
ON s.website_session_id = o.website_session_id
WHERE s.created_at < '2012-11-27'
GROUP BY 1;

# Trend Analysis

SELECT QUARTER(s.created_at) AS quarter,
    MONTHNAME(s.created_at) AS month_name,
    MONTH(s.created_at) AS month_num,
    COUNT(s.website_session_id) AS sessions,
    COUNT(order_id) AS orders,
    COUNT(order_id) / COUNT(s.website_session_id) AS conversion_rate,
    SUM(price_usd - cogs_usd) AS profit
FROM website_sessions AS s
LEFT JOIN orders AS o 
ON s.website_session_id = o.website_session_id
WHERE s.created_at < '2012-11-27'
GROUP BY 1, 2, 3;
```

</br>

### Monthly Trends for Gsearch Traffic
This page displays monthly trends in sessions, orders, and profit specifically for the “gsearch” traffic source, highlighting the growth patterns over the year.

![website_performance_and_traffic_sources_page-0002](https://github.com/gnoevoy/Ecommerce_and_Web_Analytics/assets/43414592/7621e57a-6338-4880-8390-e32df8b39c17)

```sql
# Task 2
# Calculate and present the monthly trends for sessions, orders, and profit from the "gsearch" traffic source to demonstrate its growth.

SELECT QUARTER(s.created_at) AS quarter,
    MONTHNAME(s.created_at) AS month_name,
    MONTH(s.created_at) AS month_num,
    COUNT(s.website_session_id) AS sessions,
    COUNT(order_id) AS orders,
    SUM(price_usd - cogs_usd) AS profit
FROM website_sessions AS s
LEFT JOIN orders AS o 
ON s.website_session_id = o.website_session_id
WHERE s.created_at < '2012-11-27' AND utm_source = 'gsearch'
GROUP BY 1, 2, 3;
```

</br>

### Gsearch Campaign Comparison
Focuses on the monthly trends for 'gsearch', comparing 'nonbrand' and 'brand' campaigns separately to illustrate their distinct performance dynamics.

![website_performance_and_traffic_sources_page-0003](https://github.com/gnoevoy/Ecommerce_and_Web_Analytics/assets/43414592/6accc932-ae17-40c5-bb2c-9b86f9b44265)

```sql
# Task 3
# Calculate a monthly trend analysis for 'gsearch,' and ensure it's separated into non-brand and brand campaigns.

SELECT QUARTER(s.created_at) AS quarter,
    MONTHNAME(s.created_at) AS month_name,
    MONTH(s.created_at) AS month, utm_campaign,
    COUNT(s.website_session_id) AS sessions,
    COUNT(order_id) AS orders,
    COALESCE(SUM(price_usd - cogs_usd), 0) AS profit
FROM website_sessions AS s
LEFT JOIN orders AS o 
ON s.website_session_id = o.website_session_id
WHERE s.created_at < '2012-11-27'
    AND utm_source = 'gsearch'
    AND utm_campaign IN ('nonbrand', 'brand')
GROUP BY 1, 2, 3, 4;
```

</br>

### Nonbrand Category by Device
Analyzes the 'nonbrand' category from 'gsearch', breaking down the monthly trends by different device types to understand device-specific engagement.

![website_performance_and_traffic_sources_page-0004](https://github.com/gnoevoy/Ecommerce_and_Web_Analytics/assets/43414592/a9952a4c-2914-412c-b9ce-27c92c1fb8af)

```sql
# Task 4
# Calculate monthly trends for the 'nonbrand' category, segmented by device type.

SELECT QUARTER(s.created_at) AS quarter,
    MONTHNAME(s.created_at) AS month_name,
    MONTH(s.created_at) AS month, device_type,
    COUNT(s.website_session_id) AS sessions,
    COUNT(order_id) AS orders,
    SUM(price_usd - cogs_usd) AS profit
FROM website_sessions AS s
LEFT JOIN orders AS o 
ON s.website_session_id = o.website_session_id
WHERE s.created_at < '2012-11-27'
    AND utm_source = 'gsearch'
    AND utm_campaign = 'nonbrand'
GROUP BY 1, 2, 3, 4;
```

</br>

### Conversion Trends Across Channels
Presents monthly trends of the conversion rate for “gsearch” and compares it with trends from other traffic channels to provide a comprehensive view of channel effectiveness.

![website_performance_and_traffic_sources_page-0005](https://github.com/gnoevoy/Ecommerce_and_Web_Analytics/assets/43414592/84b68847-90f7-43bd-85cf-dc322e4c2c64)

```sql
# Task 5
# Calculate monthly conversion rate trends for 'gsearch' and also generate monthly trends for each of our other channels.

SELECT QUARTER(s.created_at) AS quarter,
    MONTHNAME(s.created_at) AS month_name,
    MONTH(s.created_at) AS month,
    COALESCE(utm_source, 'direct') AS utm_source,
    COUNT(s.website_session_id) AS sessions,
    COUNT(order_id) AS orders,
    COUNT(order_id) / COUNT(s.website_session_id) AS conversion_rate
FROM website_sessions AS s
LEFT JOIN orders AS o 
ON s.website_session_id = o.website_session_id
WHERE s.created_at < '2012-11-27'
GROUP BY 1, 2, 3, 4;
```

</br>

### A/B Test Results: Homepage for Gsearch Nonbrand
Shows the outcome of the A/B test conducted on the homepage during summer, comparing the revenue earned from “gsearch nonbrand” traffic between the original and test homepages.

![website_performance_and_traffic_sources_page-0006](https://github.com/gnoevoy/Ecommerce_and_Web_Analytics/assets/43414592/2472b2bf-3d0c-43d0-a45e-9a7d863c23b0)

```sql
# Task 6
# Calculate the A/B test results for our homepage conducted between June 19 and July 28.
# Determine profit generated for 'gsearch nonbrand' and compare it to profit from the original main homepage.

WITH homepage_test AS (
    SELECT s.website_session_id, s.created_at,
        order_id, (price_usd - cogs_usd) AS profit,
        p.created_at AS pageview_date, pageview_url
    FROM website_sessions AS s
    LEFT JOIN orders AS o 
    ON s.website_session_id = o.website_session_id
    INNER JOIN website_pageviews AS p
    ON s.website_session_id = p.website_session_id
    WHERE s.created_at BETWEEN '2012-06-19' AND '2012-07-28'
        AND utm_source = 'gsearch' AND utm_campaign = 'nonbrand'
        AND pageview_url IN ('/home', '/lander-1')
)

# In our case, each distinct session has only one entry page.
# SELECT website_session_id FROM homepage_test GROUP BY 1 HAVING COUNT(DISTINCT pageview_url) > 1;

SELECT pageview_url,
    COUNT(website_session_id) AS sessions,
    COUNT(order_id) AS orders,
    COUNT(order_id) / COUNT(website_session_id) AS conversion_rate,
    SUM(profit) AS profit
FROM homepage_test
GROUP BY 1;
```

</br>

### Conversation Funnel from A/B Test
Displays the full conversion funnel from both versions of the homepage tested in the A/B test, tracking user journey from landing to orders within the specified test period.

![website_performance_and_traffic_sources_page-0007](https://github.com/gnoevoy/Ecommerce_and_Web_Analytics/assets/43414592/a94a3011-ae17-4eda-9b71-3087f83237f6)

> **Note**
> **Conversion funnel** – a series of steps that a user takes to complete a purchase on a website or app. The goal is to guide users through the funnel and maximize the number of successful conversions.
> </br>
> **Conversion Funnel Rate** = (Current page sessions / Previous page sessions) * 100

```sql
# Task 7
# Calculate a comprehensive conversion funnel from each of the two pages to orders (home and lander-1).
# Utilize the same time period you examined in our A/B test and the same utm_source and campaign.

# Step-1. Create a source table with the necessary parameters.

WITH source_table AS (
    SELECT website_pageview_id, p.created_at,
        p.website_session_id, pageview_url
    FROM website_sessions AS s
    INNER JOIN website_pageviews AS p
    ON s.website_session_id = p.website_session_id
    WHERE s.created_at BETWEEN '2012-06-19' AND '2012-07-28'
        AND utm_source = 'gsearch'
        AND utm_campaign = 'nonbrand'
),

# Step-2. Identify sessions with an entry page = 'home' and with entry page = 'lander-1' separately.

lander_1 AS (
    SELECT website_session_id AS lander_sessions
    FROM (
        SELECT *, ROW_NUMBER() OVER (PARTITION BY website_session_id ORDER BY created_at ASC) AS rnk
	FROM source_table
    ) AS t1
    WHERE pageview_url = '/lander-1' AND rnk = 1
),

home AS (
    SELECT website_session_id AS home_sessions
    FROM (
        SELECT *, ROW_NUMBER() OVER (PARTITION BY website_session_id ORDER BY created_at ASC) AS rnk
	FROM source_table
    ) AS t1
    WHERE pageview_url = '/home' AND rnk = 1
),

# Step-3. Merge 3 tables and calculate the number of sessions for each page.
# Determine the number of sessions from the previous page.

conversion_funnel AS (
    SELECT CASE WHEN pageview_url IN ('/home', '/lander-1') THEN 'entry_page' ELSE pageview_url END AS pageview_url, 
        COUNT(lander_sessions) AS lander_sessions,
        LAG(COUNT(lander_sessions)) OVER () AS previous_page_lander,
        COUNT(home_sessions) AS home_sessions,
        LAG(COUNT(home_sessions)) OVER () AS previous_page_home
    FROM source_table AS s
    LEFT JOIN lander_1 AS l ON s.website_session_id = l.lander_sessions
    LEFT JOIN home AS h ON s.website_session_id = h.home_sessions
    GROUP BY 1
)

# Step-4. Determine the click rate percentage.

SELECT pageview_url,
    lander_sessions,
    COALESCE(lander_sessions / previous_page_lander, 1) AS lander_clikrate,
    home_sessions,
    COALESCE(home_sessions / previous_page_home, 1) AS home_clikrate
FROM conversion_funnel;
```

</br>

### Bounce Rate Comparison
Compares the bounce rates of the main homepage against 'lander-1', providing insights into visitor engagement and page effectiveness.

![website_performance_and_traffic_sources_page-0008](https://github.com/gnoevoy/Ecommerce_and_Web_Analytics/assets/43414592/db04e9b0-2883-47a9-81ce-9a01d2d58151)

> **Note**
> **Bounce rate** – a metric that measures the percentage of visitors who land on a website or a specific page and then leave the site without interacting with it further.
> </br>
> **Bounce Rate** = (Number of single-page sessions) / (Total sessions) * 100

```sql
# Task 8
# Calculate the comparison of bounce rates for the main homepage and 'lander-1' test
# Use 'gsearch nonbrand' traffic source and campaign again.

# Step-1. Create a source table with the necessary parameters.

WITH souce_table AS (
    SELECT p.website_pageview_id, p.created_at,
        p.website_session_id, pageview_url
    FROM website_sessions AS s
    INNER JOIN website_pageviews AS p
    ON s.website_session_id = p.website_session_id
    WHERE s.created_at < '2012-11-27'
        AND utm_source = 'gsearch'
        AND utm_campaign = 'nonbrand'
),

# Step-2. Identify bounce sessions for both entry pages.

bounce_sessions AS (
    SELECT website_session_id
    FROM souce_table
    GROUP BY website_session_id
    HAVING COUNT(DISTINCT pageview_url) = 1
)

# Step-3. Determine the bounce rate percentage.

SELECT pageview_url,
    COUNT(s.website_session_id) AS total_sessions,
    COUNT(b.website_session_id) AS bounce_sessions,
    COUNT(b.website_session_id) / COUNT(s.website_session_id) AS bounce_rate
FROM souce_table AS s
LEFT JOIN bounce_sessions AS b 
ON s.website_session_id = b.website_session_id
WHERE pageview_url IN ('/home', '/lander-1')
GROUP BY 1;
```

</br>

### A/B Test: Billing Page Analysis
Provides results of the A/B test conducted on the billing page, with a focus on the “Revenue per click” metric for 'gsearch nonbrand', offering insights into the effectiveness of the billing page variations.

![website_performance_and_traffic_sources_page-0009](https://github.com/gnoevoy/Ecommerce_and_Web_Analytics/assets/43414592/c0a251d5-412a-4b1c-8476-2d063abe25fc)

```sql
# Task 9
# Calculate the outcomes of the A/B test on the billing page that was carried out between September 10 and November 10.
# Specifically, focus on determining the 'Revenue per click' metric for the 'gsearch nonbrand'.

WITH billing_test AS (
    SELECT s.website_session_id, order_id,
        price_usd AS revenue, p.created_at AS pageview_date,
        pageview_url
    FROM website_sessions AS s
    LEFT JOIN orders AS o 
    ON s.website_session_id = o.website_session_id
    INNER JOIN website_pageviews AS p
    ON s.website_session_id = p.website_session_id
    WHERE s.created_at BETWEEN '2012-09-10' AND '2012-11-10'
        AND utm_source = 'gsearch' AND utm_campaign = 'nonbrand'
        AND pageview_url IN ('/billing', '/billing-2')
)

# Like in task 6, there are no any sessions who visited both billing pages.
# SELECT website_session_id FROM billing_test GROUP BY 1 HAVING COUNT(DISTINCT pageview_url) > 1;

SELECT pageview_url,
    COUNT(website_session_id) AS sessions,
    COUNT(order_id) AS orders,
    COUNT(order_id) / COUNT(website_session_id) AS conversion_rate,
    SUM(revenue) AS revenue,
    SUM(revenue) / COUNT(website_session_id) AS revenue_per_click
FROM billing_test
GROUP BY 1;
```

</br>

### Navigation links
- [Customers and Products Analysis](https://github.com/gnoevoy/Ecommerce_and_Web_Analytics/blob/main/Assignments%20/Customers_and_products.md)
- [README.md](https://github.com/gnoevoy/Ecommerce_and_Web_Analytics/blob/main/README.md)

















