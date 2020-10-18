# Insights from Data with BigQuery: Challenge Lab

The dataset that we would be using in this challenge lab is bigquery-public-data.covid19_open_data.covid19_open_data. This dataset contains data related to covid-19 on a country basis globally. We would be using this in this skill badge tutorial.

## Loading the Dataset:

1-In the cloud console, once logged in completely, Go to Menu > BigQuery.

2-Click + Add Data and then click on Explore Public Datasets from the left pane.

3-Search 'covid19_open_data' and then select “Covid-19 Open Data”. Click on View Dataset to explore more!

4-Use filter and locate the table 'covid19_open_data' under the 'covid19_open_data'  dataset.

## Task — 1

Copy the below query in the query editor and click on RUN:

    SELECT
      SUM(cumulative_confirmed) AS total_cases_worldwide
    FROM
      `bigquery-public-data.covid19_open_data.covid19_open_data`
    WHERE
      date = "2020-04-15"
      
##  Task — 2:

Copy the below query in the query editor and click on RUN:

    SELECT
      COUNT(*) AS count_of_states
    FROM (
    SELECT
      subregion1_name AS state,
      SUM(cumulative_deceased) AS death_count
    FROM
      `bigquery-public-data.covid19_open_data.covid19_open_data`
    WHERE
      country_name="United States of America"
    AND date='2020-04-10'
    AND subregion1_name IS NOT NULL
    GROUP BY
      subregion1_name
         )
    WHERE death_count > 100
    
## Task — 3:

Copy the below query in the query editor and click on RUN:

    SELECT * FROM (
    SELECT subregion1_name as state, sum(cumulative_confirmed) as total_confirmed_cases
    FROM `bigquery-public-data.covid19_open_data.covid19_open_data`
    WHERE country_code="US" AND date='2020-04-10' AND subregion1_name is NOT NULL
    GROUP BY subregion1_name
    ORDER BY total_confirmed_cases DESC ) WHERE total_confirmed_cases > 1000
    
## Task 4-

Copy the below query in the query editor and click on RUN:

    SELECT SUM(cumulative_confirmed) AS total_confirmed_cases,
    SUM(cumulative_deceased) AS total_deaths,
    (SUM(cumulative_deceased)/SUM(cumulative_confirmed))*100 AS case_fatality_ratio
    FROM `bigquery-public-data.covid19_open_data.covid19_open_data`
    WHERE country_name="Italy" AND date BETWEEN "2020-04-01" AND "2020-04-30"
    
    
## Task 5

Copy the below query in the query editor and click on RUN;

    SELECT
      date
    FROM
      `bigquery-public-data.covid19_open_data.covid19_open_data`
    WHERE
      country_name = 'Italy'
    AND cumulative_deceased > 10000
    ORDER BY date
    LIMIT 1
    
## Task 6 

Copy the below query in the query editor and click on RUN:

    WITH india_cases_by_date AS (
    SELECT
      date,
    SUM(cumulative_confirmed) AS cases
    FROM
      `bigquery-public-data.covid19_open_data.covid19_open_data`
    WHERE
      country_name="India"
    AND date between '2020-02-21' and '2020-03-15'
    GROUP BY
      date
    ORDER BY
      date ASC
    )

    , india_previous_day_comparison AS
    (SELECT
      date,
      cases,
      LAG(cases) OVER(ORDER BY date) AS previous_day,
      cases - LAG(cases) OVER(ORDER BY date) AS net_new_cases
    FROM india_cases_by_date
    )
    SELECT
      COUNT(date)
    FROM
      india_previous_day_comparison
    WHERE
      net_new_cases = 0
      
## Task — 7

Copy the below query in the query editor and click on RUN:

    WITH us_cases_by_date AS (
    SELECT
      date,
      SUM( cumulative_confirmed ) AS cases
    FROM
      `bigquery-public-data.covid19_open_data.covid19_open_data`
    WHERE
      country_name="United States of America"
      AND date between '2020-03-22' and '2020-04-20'
    GROUP BY
    date
    ORDER BY
      date ASC
    )

    , us_previous_day_comparison AS
    (SELECT
      date,
      cases,
      LAG(cases) OVER(ORDER BY date) AS previous_day,
      cases - LAG(cases) OVER(ORDER BY date) AS net_new_cases,
      (cases - LAG(cases) OVER(ORDER BY date))*100/LAG(cases) OVER(ORDER BY date) AS percentage_increase
    FROM us_cases_by_date
    )
    SELECT
      Date,
      cases AS Confirmed_Cases_On_Day,
      previous_day AS Confirmed_Cases_Previous_Day,
      percentage_increase AS Percentage_Increase_In_Cases
    FROM
      us_previous_day_comparison
    WHERE
      percentage_increase > 10
      
## Task — 8

Copy the below query in the query editor and click on RUN:

    WITH cases_by_country AS (
    SELECT
      country_name AS country,
      SUM(cumulative_confirmed) AS cases,
      SUM(cumulative_recovered) AS recovered_cases
    FROM
      `bigquery-public-data.covid19_open_data.covid19_open_data`
    WHERE
      date="2020-05-10"
    GROUP BY
      country_name
    )

    , recovered_rate AS (
    SELECT
       country, cases, recovered_cases,
       (recovered_cases * 100)/cases AS recovery_rate
    FROM
        cases_by_country
    )

    SELECT country, cases AS confirmed_cases, recovered_cases, recovery_rate
    FROM
      recovered_rate
    WHERE
      cases > 50000
    ORDER BY recovery_rate DESC
    LIMIT 10
    

    
## Task — 10

1- First of all, Copy the below query in the query editor and click on RUN:

    SELECT
      date, SUM(cumulative_confirmed) AS country_cases,
      SUM(cumulative_deceased) AS country_deaths
    FROM
    `bigquery-public-data.covid19_open_data.covid19_open_data`
    WHERE
      date BETWEEN '2020-03-15'
      AND '2020-04-30'
      AND country_name='United States of America'
    GROUP BY date
    
2-Click on EXPLORE DATA > Explore with Data Studio.

3-Give access to Data Studio and authorize it to control BigQuery.

4- Create a new Time series chart in the new Data Studio report by selecting Add a chart > Time series Chart.

5-Add 'country_cases' and 'country_deaths' to the Metric field (Drag and Drop).

6-Click Save to commit the change.
