---
title: "Pups and Pubs - Brewery Analysis With SQL, Python, and Tableau"
date: 2023-07-22
categories:
  - Project
tags:
  - SQL
  - Tableau
  - Python
  - Pandas
  - BeautifulSoup
classes: wide
mathjax: true
#toc: true
#toc_sticky: true
#toc_label: Table of Contents
---

Name a better combo than dogs, sunny weather, and a cold beverage. That's the goal whenever I'm eager to try out a new brewery with my friends and family. Our dog Cassie loves to come on trips with us, and exploring new breweries is no exception. In Michigan, it's an easy task finding something new to try as breweries have rapidly opened up in the Great Lakes State. While there's never a shortage of breweries to experience, finding dog-friendly breweries is not always as simple. In this project, we'll explore a database full of brewery data to see what insights we can extract. At the end, we'll introduce a second data source to help us locate dog-friendly breweries in Michigan. We'll finish the project by building an interactive map of our dog-friendly breweries.

<figure>
  <img src="//assets/images/IMG_0182.jpeg">
  <figcaption>Image: Cassie at Hazel Park High School tennis courts. Credit: Emma Weintraub</figcaption>
</figure>

![jpeg](/assets/images/IMG_0182.jpeg)

*Image: Cassie at Hazel Park High School tennis courts. Credit: Emma Weintraub*

#### `breweries`

| column               | type         | description               |
|----------------------|------------------------------------------|----------------------------------|
| `brewery_id`         | text         | unique brewery id         |
| `brewery_name`       | text         | name of brewery                   |
| `brewery_type`       | text         | type of brewery (brewpub, micro, etc.)  |
| `address_1`          | text         | building address         |
| `address_2`          | text         | alternative address          |
| `address_3`          | text         | alternative address     |
| `city`               | text         | city of brewery  |
| `state_province`     | text         | state or province of brewery  |
| `postal_code`        | text         | postal code of brewery |
| `country`            | text         | country of brewery |
| `phone`              | text         | phone number of brewery |
| `website_url`        | text         | website of brewery |
| `longitude`          | numeric      | longitude coordinates |
| `latitude`           | numeric      | latitude coordinates |

We'll start by using python libraries `pandas`, `psycopg2`, and `sqlalchemy` to retrieve our breweries data from Open Brewery DB's GitHub repository (3).


```python
import pandas as pd
import psycopg2 as pg2
from sqlalchemy import create_engine
```



```python
conn = pg2.connect(database=database, user=user,password=password)
cur = conn.cursor()

engine = create_engine(engine_string)

url = ('https://raw.githubusercontent.com/openbrewerydb/openbrewerydb/master/breweries.csv')
df = pd.read_csv(url)

df.to_sql('breweries',con=engine, if_exists='replace')
```




    206



Let's begin by getting a sample of 5 breweries from our database to make sure the data was successfully loaded. We'll then observe the samples to get an idea of what our data looks like.


```sql
%%sql

-- Get the first 5 rows from the breweries table
SELECT *
FROM breweries
LIMIT 5
```


<span style="None">Running query in &#x27;brewery_database&#x27;</span>



<span style="color: green">5 rows affected.</span>





<table>
    <thead>
        <tr>
            <th>index</th>
            <th>id</th>
            <th>name</th>
            <th>brewery_type</th>
            <th>address_1</th>
            <th>address_2</th>
            <th>address_3</th>
            <th>city</th>
            <th>state_province</th>
            <th>postal_code</th>
            <th>country</th>
            <th>phone</th>
            <th>website_url</th>
            <th>longitude</th>
            <th>latitude</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>0</td>
            <td>5128df48-79fc-4f0f-8b52-d06be54d0cec</td>
            <td>(405) Brewing Co</td>
            <td>micro</td>
            <td>1716 Topeka St</td>
            <td>None</td>
            <td>None</td>
            <td>Norman</td>
            <td>Oklahoma</td>
            <td>73069-8224</td>
            <td>United States</td>
            <td>4058160490</td>
            <td><a href=http://www.405brewing.com>http://www.405brewing.com</a></td>
            <td>-97.46818222</td>
            <td>35.25738891</td>
        </tr>
        <tr>
            <td>1</td>
            <td>9c5a66c8-cc13-416f-a5d9-0a769c87d318</td>
            <td>(512) Brewing Co</td>
            <td>micro</td>
            <td>407 Radam Ln Ste F200</td>
            <td>None</td>
            <td>None</td>
            <td>Austin</td>
            <td>Texas</td>
            <td>78745-1197</td>
            <td>United States</td>
            <td>5129211545</td>
            <td><a href=http://www.512brewing.com>http://www.512brewing.com</a></td>
            <td>None</td>
            <td>None</td>
        </tr>
        <tr>
            <td>2</td>
            <td>ef970757-fe42-416f-931d-722451f1f59c</td>
            <td>10 Barrel Brewing Co</td>
            <td>large</td>
            <td>1501 E St</td>
            <td>None</td>
            <td>None</td>
            <td>San Diego</td>
            <td>California</td>
            <td>92101-6618</td>
            <td>United States</td>
            <td>6195782311</td>
            <td><a href=http://10barrel.com>http://10barrel.com</a></td>
            <td>-117.129593</td>
            <td>32.714813</td>
        </tr>
        <tr>
            <td>3</td>
            <td>6d14b220-8926-4521-8d19-b98a2d6ec3db</td>
            <td>10 Barrel Brewing Co</td>
            <td>large</td>
            <td>62970 18th St</td>
            <td>None</td>
            <td>None</td>
            <td>Bend</td>
            <td>Oregon</td>
            <td>97701-9847</td>
            <td>United States</td>
            <td>5415851007</td>
            <td><a href=http://www.10barrel.com>http://www.10barrel.com</a></td>
            <td>-121.281706</td>
            <td>44.08683531</td>
        </tr>
        <tr>
            <td>4</td>
            <td>e2e78bd8-80ff-4a61-a65c-3bfbd9d76ce2</td>
            <td>10 Barrel Brewing Co</td>
            <td>large</td>
            <td>1135 NW Galveston Ave Ste B</td>
            <td>None</td>
            <td>None</td>
            <td>Bend</td>
            <td>Oregon</td>
            <td>97703-2465</td>
            <td>United States</td>
            <td>5415851007</td>
            <td>None</td>
            <td>-121.3288021</td>
            <td>44.0575649</td>
        </tr>
    </tbody>
</table>



Looks like our python script loaded the data into our PostgreSQL database successfully! We can see all columns present as well as 5 sample data points. We can also see that there's null values in our dataset, but that's ok! When creating a new table, we can designate which columns we absolutely will not allow null values. Before loading the data into PostgreSQL, I wrote a query to make sure a table was generated with no null values allowed for `brewery_id` (primary key), `brewery_name`, `city`, `state_province`, and `country`. These will be the primary values we'll need. Now, we're ready to explore our data!

## 1. How many breweries are in the database?


```sql
%%sql

-- Get the total count of rows from breweries
SELECT COUNT(*)
FROM breweries
```


<span style="None">Running query in &#x27;brewery_database&#x27;</span>



<span style="color: green">1 rows affected.</span>





<table>
    <thead>
        <tr>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>8206</td>
        </tr>
    </tbody>
</table>



Looks like our database has 8206 total breweries listed from around the globe! That's a lot of breweries.

## 2. How many breweries are there in each country?


```sql
%%sql

-- Get all countries and respective brewery counts from breweries in descending order
SELECT country, COUNT(*) as cnt
FROM breweries
GROUP BY country
ORDER BY cnt DESC
```


<span style="None">Running query in &#x27;brewery_database&#x27;</span>



<span style="color: green">11 rows affected.</span>





<table>
    <thead>
        <tr>
            <th>country</th>
            <th>cnt</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>United States</td>
            <td>7935</td>
        </tr>
        <tr>
            <td>Ireland</td>
            <td>70</td>
        </tr>
        <tr>
            <td>England</td>
            <td>62</td>
        </tr>
        <tr>
            <td>South Korea</td>
            <td>61</td>
        </tr>
        <tr>
            <td>Poland</td>
            <td>34</td>
        </tr>
        <tr>
            <td>Austria</td>
            <td>14</td>
        </tr>
        <tr>
            <td>Portugal</td>
            <td>14</td>
        </tr>
        <tr>
            <td>Scotland</td>
            <td>10</td>
        </tr>
        <tr>
            <td>France</td>
            <td>3</td>
        </tr>
        <tr>
            <td>Isle of Man</td>
            <td>2</td>
        </tr>
        <tr>
            <td> United States</td>
            <td>1</td>
        </tr>
    </tbody>
</table>



Wow! The US has over 7935 breweries! That is quite a lead compared to all other countries listed. Another interesting observation is that the database shows two different 'United States' countries listed. Why is that? Let's take a look at the dataframe in python to find out.


```python
df['country'].unique()
```




    array(['United States', 'Ireland', 'England', 'South Korea', 'Portugal',
           'Poland', 'France', 'Isle of Man', 'Scotland', 'Austria',
           ' United States'], dtype=object)



It appears that there is an extra space present in one of the strings. Let's apply a `trim` function in SQL to clean that column.


```sql
%%sql

--remove beginning and ending spaces on each country string
UPDATE breweries
SET country = btrim (country, ' ')
```


<span style="None">Running query in &#x27;brewery_database&#x27;</span>



<span style="color: green">8206 rows affected.</span>





    




```sql
%%sql

-- Get the US brewery count to check if the cleaning operation was successfull. 7935 -> 7936
SELECT country, COUNT(*)
FROM breweries
WHERE country = 'United States'
GROUP BY country
```


<span style="None">Running query in &#x27;brewery_database&#x27;</span>



<span style="color: green">1 rows affected.</span>





<table>
    <thead>
        <tr>
            <th>country</th>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>United States</td>
            <td>7936</td>
        </tr>
    </tbody>
</table>



## 3. How many breweries are there in each state in the US?


```sql
%%sql

-- get all US states and respective brewery counts from breweries
SELECT state_province, COUNT(*) as cnt
FROM breweries
WHERE country = 'United States'
GROUP BY state_province
ORDER BY cnt DESC
```


<span style="None">Running query in &#x27;brewery_database&#x27;</span>



<span style="color: green">54 rows affected.</span>





<table>
    <thead>
        <tr>
            <th>state_province</th>
            <th>cnt</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>California</td>
            <td>912</td>
        </tr>
        <tr>
            <td>Washington</td>
            <td>471</td>
        </tr>
        <tr>
            <td>Colorado</td>
            <td>431</td>
        </tr>
        <tr>
            <td>New York</td>
            <td>418</td>
        </tr>
        <tr>
            <td>Michigan</td>
            <td>375</td>
        </tr>
        <tr>
            <td>Texas</td>
            <td>352</td>
        </tr>
        <tr>
            <td>Pennsylvania</td>
            <td>345</td>
        </tr>
        <tr>
            <td>Florida</td>
            <td>312</td>
        </tr>
        <tr>
            <td>North Carolina</td>
            <td>307</td>
        </tr>
        <tr>
            <td>Ohio</td>
            <td>303</td>
        </tr>
        <tr>
            <td>Oregon</td>
            <td>295</td>
        </tr>
        <tr>
            <td>Virginia</td>
            <td>254</td>
        </tr>
        <tr>
            <td>Illinois</td>
            <td>249</td>
        </tr>
        <tr>
            <td>Wisconsin</td>
            <td>217</td>
        </tr>
        <tr>
            <td>Minnesota</td>
            <td>182</td>
        </tr>
        <tr>
            <td>Massachusetts</td>
            <td>163</td>
        </tr>
        <tr>
            <td>Indiana</td>
            <td>162</td>
        </tr>
        <tr>
            <td>Missouri</td>
            <td>141</td>
        </tr>
        <tr>
            <td>Arizona</td>
            <td>124</td>
        </tr>
        <tr>
            <td>New Jersey</td>
            <td>115</td>
        </tr>
        <tr>
            <td>Maine</td>
            <td>114</td>
        </tr>
        <tr>
            <td>Tennessee</td>
            <td>110</td>
        </tr>
        <tr>
            <td>Maryland</td>
            <td>109</td>
        </tr>
        <tr>
            <td>Georgia</td>
            <td>100</td>
        </tr>
        <tr>
            <td>Connecticut</td>
            <td>94</td>
        </tr>
        <tr>
            <td>Montana</td>
            <td>92</td>
        </tr>
        <tr>
            <td>Iowa</td>
            <td>91</td>
        </tr>
        <tr>
            <td>New Mexico</td>
            <td>83</td>
        </tr>
        <tr>
            <td>South Carolina</td>
            <td>79</td>
        </tr>
        <tr>
            <td>New Hampshire</td>
            <td>72</td>
        </tr>
        <tr>
            <td>Idaho</td>
            <td>67</td>
        </tr>
        <tr>
            <td>Vermont</td>
            <td>59</td>
        </tr>
        <tr>
            <td>Kentucky</td>
            <td>58</td>
        </tr>
        <tr>
            <td>Nebraska</td>
            <td>57</td>
        </tr>
        <tr>
            <td>Nevada</td>
            <td>51</td>
        </tr>
        <tr>
            <td>Alaska</td>
            <td>51</td>
        </tr>
        <tr>
            <td>Kansas</td>
            <td>47</td>
        </tr>
        <tr>
            <td>Arkansas</td>
            <td>45</td>
        </tr>
        <tr>
            <td>Alabama</td>
            <td>45</td>
        </tr>
        <tr>
            <td>South Dakota</td>
            <td>45</td>
        </tr>
        <tr>
            <td>Oklahoma</td>
            <td>44</td>
        </tr>
        <tr>
            <td>Utah</td>
            <td>44</td>
        </tr>
        <tr>
            <td>Louisiana</td>
            <td>43</td>
        </tr>
        <tr>
            <td>West Virginia</td>
            <td>33</td>
        </tr>
        <tr>
            <td>Wyoming</td>
            <td>32</td>
        </tr>
        <tr>
            <td>Rhode Island</td>
            <td>31</td>
        </tr>
        <tr>
            <td>Delaware</td>
            <td>28</td>
        </tr>
        <tr>
            <td>North Dakota</td>
            <td>26</td>
        </tr>
        <tr>
            <td>Hawaii</td>
            <td>23</td>
        </tr>
        <tr>
            <td>Mississippi</td>
            <td>16</td>
        </tr>
        <tr>
            <td>District of Columbia</td>
            <td>16</td>
        </tr>
        <tr>
            <td> Utah</td>
            <td>1</td>
        </tr>
        <tr>
            <td>MIssouri</td>
            <td>1</td>
        </tr>
        <tr>
            <td>Washington </td>
            <td>1</td>
        </tr>
    </tbody>
</table>



Look at that! Michigan is in the top 5 states in the US regarding total number of breweries. Michigan is ranked 5th behind New York, Colorado, Washington, and California. Let's dive deeper into the Michigan brewery scene. 

## 4. What are the brewery types in Michigan?


```sql
%%sql

-- Get the brewery type and respective count from Michigan, US breweries in descending order
SELECT brewery_type, COUNT(*) AS total_breweries
FROM breweries
WHERE country = 'United States'
AND state_province = 'Michigan'
GROUP BY brewery_type
ORDER BY total_breweries DESC
```


<span style="None">Running query in &#x27;brewery_database&#x27;</span>



<span style="color: green">5 rows affected.</span>





<table>
    <thead>
        <tr>
            <th>brewery_type</th>
            <th>total_breweries</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>brewpub</td>
            <td>199</td>
        </tr>
        <tr>
            <td>micro</td>
            <td>143</td>
        </tr>
        <tr>
            <td>planning</td>
            <td>21</td>
        </tr>
        <tr>
            <td>regional</td>
            <td>9</td>
        </tr>
        <tr>
            <td>contract</td>
            <td>3</td>
        </tr>
    </tbody>
</table>



The majority of breweries in Michigan are brewpubs closely followed by micro breweries. This makes sense if we consider that brewpubs are beer-focused restaurants and micro breweries are what most craft breweries are considered.

## 5. What cities in Michigan have the most breweries?


```sql
%%sql

-- Get the top 5 cities and respective brewery count from Michigan, US breweries in descending order
SELECT city, COUNT(*) AS total_breweries
FROM breweries
WHERE country = 'United States'
AND state_province = 'Michigan'
GROUP BY city
ORDER BY total_breweries DESC
LIMIT 5
```


<span style="None">Running query in &#x27;brewery_database&#x27;</span>



<span style="color: green">5 rows affected.</span>





<table>
    <thead>
        <tr>
            <th>city</th>
            <th>total_breweries</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Grand Rapids</td>
            <td>25</td>
        </tr>
        <tr>
            <td>Kalamazoo</td>
            <td>14</td>
        </tr>
        <tr>
            <td>Detroit</td>
            <td>13</td>
        </tr>
        <tr>
            <td>Traverse City</td>
            <td>12</td>
        </tr>
        <tr>
            <td>Ann Arbor</td>
            <td>11</td>
        </tr>
    </tbody>
</table>



We can see that the top 5 cities in Michigan with the most breweries includes Grand Rapids, Kalamazoo, Detroit, Traverse City, and Ann Arbor. Grand Rapids appears to be the top city for breweries by a large margin (11 more than the next leading city Kalamazoo). 

## 6. What is the breakdown of brewery types in the top 5 cities in Michigan?


```sql
%%sql

-- Get the city, brewery type, and respective brewery total from Michigan breweries grouped by city and type. Query ordered by city and brewery totals.
SELECT city, brewery_type, COUNT(*) AS total_breweries
FROM breweries
WHERE country = 'United States'
AND state_province = 'Michigan'
AND city IN ('Grand Rapids', 'Kalamazoo', 'Detroit', 'Traverse City', 'Ann Arbor')
GROUP BY city, brewery_type
ORDER BY city, total_breweries DESC
```


<span style="None">Running query in &#x27;brewery_database&#x27;</span>



<span style="color: green">16 rows affected.</span>





<table>
    <thead>
        <tr>
            <th>city</th>
            <th>brewery_type</th>
            <th>total_breweries</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Ann Arbor</td>
            <td>brewpub</td>
            <td>8</td>
        </tr>
        <tr>
            <td>Ann Arbor</td>
            <td>micro</td>
            <td>2</td>
        </tr>
        <tr>
            <td>Ann Arbor</td>
            <td>planning</td>
            <td>1</td>
        </tr>
        <tr>
            <td>Detroit</td>
            <td>brewpub</td>
            <td>5</td>
        </tr>
        <tr>
            <td>Detroit</td>
            <td>micro</td>
            <td>3</td>
        </tr>
        <tr>
            <td>Detroit</td>
            <td>regional</td>
            <td>2</td>
        </tr>
        <tr>
            <td>Detroit</td>
            <td>planning</td>
            <td>2</td>
        </tr>
        <tr>
            <td>Detroit</td>
            <td>contract</td>
            <td>1</td>
        </tr>
        <tr>
            <td>Grand Rapids</td>
            <td>brewpub</td>
            <td>21</td>
        </tr>
        <tr>
            <td>Grand Rapids</td>
            <td>micro</td>
            <td>2</td>
        </tr>
        <tr>
            <td>Grand Rapids</td>
            <td>regional</td>
            <td>2</td>
        </tr>
        <tr>
            <td>Kalamazoo</td>
            <td>brewpub</td>
            <td>8</td>
        </tr>
        <tr>
            <td>Kalamazoo</td>
            <td>micro</td>
            <td>5</td>
        </tr>
        <tr>
            <td>Kalamazoo</td>
            <td>planning</td>
            <td>1</td>
        </tr>
        <tr>
            <td>Traverse City</td>
            <td>micro</td>
            <td>7</td>
        </tr>
        <tr>
            <td>Traverse City</td>
            <td>brewpub</td>
            <td>5</td>
        </tr>
    </tbody>
</table>



We can see that Grand Rapids has the largest concentration of any one brewery type with 21 brewpubs, but detroit has the largest variation including a brewpub, micro, regional, planning, and contract brewery.

## 7. If we pick a city, can we find which breweries are in a 50 mile radius?

Let's say that we are visiting my home town (Midland, MI), could we find out what the nearest breweries would be? We will use Midland's latitude and longitude coordinates (43.618183 and -84.234238 respectively). Let's first review how to find the distance between 2 points on earth in miles. 

**The equation (1) is defined as:**

>$$Distance = 3963 * arccos((sin(latitude_1) * sin(latitude_2)) + cos(latitude_1) * cos(latitude_2) * cos(longitude_2 - longitude_1))$$




```sql
%%sql

-- Get the name, city, address, state, postal code, lat and long coordinates where there location is within 50 miles of Midland, MI
SELECT name, city, address_1, state_province AS state, postal_code, latitude, longitude
FROM breweries
WHERE 3963 * ACOS((SIN(radians (43.618183)) * SIN(radians (latitude))) + 
    COS(radians (43.618183)) * COS(radians (latitude)) * COS(radians (longitude) - radians (-84.234238))) 
    <= 50
```


<span style="None">Running query in &#x27;brewery_database&#x27;</span>



<span style="color: green">12 rows affected.</span>





<table>
    <thead>
        <tr>
            <th>name</th>
            <th>city</th>
            <th>address_1</th>
            <th>state</th>
            <th>postal_code</th>
            <th>latitude</th>
            <th>longitude</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Alma Brewing Co</td>
            <td>Alma</td>
            <td>208 E Superior St</td>
            <td>Michigan</td>
            <td>48801-1819</td>
            <td>43.37888808</td>
            <td>-84.6588719</td>
        </tr>
        <tr>
            <td>Cranker's Restaurant & Brewery</td>
            <td>Mount Pleasant</td>
            <td>1207 E Pickard St</td>
            <td>Michigan</td>
            <td>48858-1901</td>
            <td>43.61176916</td>
            <td>-84.76071102</td>
        </tr>
        <tr>
            <td>Four Leaf Brewing</td>
            <td>Clare</td>
            <td>412 N McEwan St</td>
            <td>Michigan</td>
            <td>48617-1403</td>
            <td>43.8256884</td>
            <td>-84.7683591</td>
        </tr>
        <tr>
            <td>Frankenmuth Brewing Co</td>
            <td>Frankenmuth</td>
            <td>425 S Main St</td>
            <td>Michigan</td>
            <td>48734-1615</td>
            <td>43.32893336</td>
            <td>-83.73929404</td>
        </tr>
        <tr>
            <td>Loggers Brewing Company</td>
            <td>Saginaw</td>
            <td>1215 S River Rd</td>
            <td>Michigan</td>
            <td>48609-5208</td>
            <td>43.40518225</td>
            <td>-84.05193542</td>
        </tr>
        <tr>
            <td>Midland Brewing Co</td>
            <td>Midland</td>
            <td>5011 N Saginaw Road</td>
            <td>Michigan</td>
            <td>48642</td>
            <td>43.6408678</td>
            <td>-84.2910925</td>
        </tr>
        <tr>
            <td>Mountain Town Brewing Co</td>
            <td>Mt Pleasant</td>
            <td>614 W Pickard St</td>
            <td>Michigan</td>
            <td>48858-1504</td>
            <td>43.61180948</td>
            <td>-84.78689552</td>
        </tr>
        <tr>
            <td>Mountain Town Station Restaurant & Brew Pub</td>
            <td>Mt Pleasant</td>
            <td>506 W Broadway St</td>
            <td>Michigan</td>
            <td>48858-2441</td>
            <td>43.604568</td>
            <td>-84.7822205</td>
        </tr>
        <tr>
            <td>Oracle Brewing Company</td>
            <td>Saginaw</td>
            <td>122 N Michigan Ave</td>
            <td>Michigan</td>
            <td>48602-4234</td>
            <td>43.417723</td>
            <td>-83.96401351</td>
        </tr>
        <tr>
            <td>Tenacity Brewing</td>
            <td>Flint</td>
            <td>119 N Grand Traverse St</td>
            <td>Michigan</td>
            <td>48503-5620</td>
            <td>43.01522788</td>
            <td>-83.69663179</td>
        </tr>
        <tr>
            <td>The Highway Brewing Co.</td>
            <td>West Branch</td>
            <td>209 W Houghton Ave</td>
            <td>Michigan</td>
            <td>48661-1219</td>
            <td>44.27635208</td>
            <td>-84.23751651</td>
        </tr>
        <tr>
            <td>The Maple Grille LLC</td>
            <td>Hemlock</td>
            <td>13105 Gratiot Rd</td>
            <td>Michigan</td>
            <td>48626-8444</td>
            <td>43.4157758</td>
            <td>-84.1741049</td>
        </tr>
    </tbody>
</table>



## 8. Which breweries are dog-friendly in Michigan?

What if we wanted to go on a road trip and check out some breweries on the way, but we also wanted to bring our dog? That data is not currently in our brewery database, so we will need to look elsewhere to find the data. Luckily, we can use Kevin McKinney's currated list of dog friendly Michigan breweries (2)! We'll first need to scrape the website and grab the needed data using python's `beautifulsoup` library. We'll clean the data to remove any trailing spaces or unnecessary characters, then we'll upload the data into a new table in our SQL database.


```python
import requests
import bs4
import re

# Pull the raw html from the website
res = requests.get("https://michiganbreweryreview.com/michigan-dog-friendly-breweries/2018/8/2/the-ever-growing-list-of-k-9-friendly-breweries?format=amp")
soup = bs4.BeautifulSoup(res.text,"lxml")

dog_brewery_list = []
cleaned_soup = []

#parse the html data looking for brewery names and the respective cities. Place the items into a cleaned_soup list
it = iter(soup.select('p')[1].contents)

for item in it:
    if item.text == "" or item.text == " " or item.text == ".":
        next
    else:
        cleaned_soup.append(item)

#iterate through the clean_soup list and remove any unnecessary spaces, dashes, or parenthesis. Place the cleaned items into a dog_brewery_list
it = iter(cleaned_soup)  

for item in it:
    dog_brewery_list.append((item.text.strip(),re.sub("[\(\[].*?[\)\]]", "", next(it).text.split('- ',1)[-1].split('- ',1)[-1].strip().strip())))

#Create a dataframe from dog_brewery_list with columns: 'brewery_name' and 'city'. Send the dataframe to posgresql as a new table
dog_df = pd.DataFrame(dog_brewery_list,columns = ['brewery_name','city'])
dog_df.to_sql('dog_breweries',con=engine, if_exists='replace')
```




    136



We now have two tables in our database. The two tables include:

#### `breweries`

| column               | type         | description               |
|----------------------|------------------------------------------|----------------------------------|
| `brewery_id`         | text         | unique brewery id         |
| `brewery_name`       | text         | name of brewery                   |
| `brewery_type`       | text         | type of brewery (brewpub, micro, etc.)  |
| `address_1`          | text         | building address         |
| `address_2`          | text         | alternative address          |
| `address_3`          | text         | alternative address     |
| `city`               | text         | city of brewery  |
| `state_province`     | text         | state or province of brewery  |
| `postal_code`        | text         | postal code of brewery |
| `country`            | text         | country of brewery |
| `phone`              | text         | phone number of brewery |
| `website_url`        | text         | website of brewery |
| `longitude`          | numeric      | longitude coordinates |
| `latitude`           | numeric      | latitude coordinates |




#### `dog_breweries`

| column               | type         | description               |
|----------------------|------------------------------------------|----------------------------------|
| `brewery_name`       | text         | name of brewery                   |
| `city`               | text         | city of brewery  |

We need to join our two tables together to determine which breweries are dog-friendly from our original breweries table. Before we conduct our join, we will clean the columns that we'll join on to make sure we can find the proper match in both tables.


```sql
%%sql

-- trim both city and name string sides of all spaces and replace any dashes or spaces with empty space on the breweries table
UPDATE breweries
SET name = rtrim (btrim (replace (replace (name, '-', ' '), '.', ''), ' '), '.'), city = btrim (city, ' ')
```


<span style="None">Running query in &#x27;brewery_database&#x27;</span>



<span style="color: green">8206 rows affected.</span>





    




```sql
%%sql

-- trim both city and name string sides of all spaces and replace any dashes or spaces with empty space on the dog_breweries table
UPDATE dog_breweries
SET brewery_name = rtrim (btrim (replace (replace (brewery_name, '-', ' '), '.', ''), ' '), '.'), city = btrim (city, ' ')
```


<span style="None">Running query in &#x27;brewery_database&#x27;</span>



<span style="color: green">136 rows affected.</span>





    




```sql
%%sql

-- Replace 'St.' with 'Saint' under the city column in the dog_breweries table
UPDATE dog_breweries
SET city = replace (city, 'St. Clair Shores', 'Saint Clair Shores')
```


<span style="None">Running query in &#x27;brewery_database&#x27;</span>



<span style="color: green">136 rows affected.</span>





    



What we want to accomplish is an `INNER JOIN` between the two tables to only include breweries that both tables share. In doing so, we will only include breweries that are dog friendly while also now having access to their locations. Unfortunately, this is not as easy of a task as it seems. While the `breweries` table has unique id's for each brewery, the `dog_breweries` does not. Because of this, we will need to match the tables based on their names. This may seem straight-forward but there's a lot that can go wrong such as mispellings, mix-matched words, additional spaces, etc. Any of these would result in a failed match. Thankfully, we can use fuzzy string matching to help us combat this issue. We'll look for words that are similar to one another instead of perfect matches. For extra accuracy, we'll also only match breweries from both tables if both cities and the first word in the name also match.


```sql
%%sql

-- First, select all breweries in Michigan that are not 'TBD'. 
-- Then, grab all breweries in michigan where the names are similar, the cities match, and the first word in each name matches
SELECT *
FROM
   (SELECT *
    FROM breweries
    WHERE breweries.state_province = 'Michigan'
    AND breweries.name <> 'TBD') AS mich_brew
JOIN dog_breweries ON SIMILARITY (mich_brew.name, dog_breweries.brewery_name) > 0.2
and mich_brew.city = dog_breweries.city
and UPPER(SUBSTRING(mich_brew.name, 1, POSITION(' ' IN mich_brew.name))) = 
    UPPER(SUBSTRING(dog_breweries.brewery_name, 1, POSITION(' ' IN dog_breweries.brewery_name)))
ORDER BY mich_brew.name
```


<span style="None">Running query in &#x27;brewery_database&#x27;</span>



<span style="color: green">98 rows affected.</span>





<table>
    <thead>
        <tr>
            <th>index</th>
            <th>id</th>
            <th>name</th>
            <th>brewery_type</th>
            <th>address_1</th>
            <th>address_2</th>
            <th>address_3</th>
            <th>city</th>
            <th>state_province</th>
            <th>postal_code</th>
            <th>country</th>
            <th>phone</th>
            <th>website_url</th>
            <th>longitude</th>
            <th>latitude</th>
            <th>index_1</th>
            <th>brewery_name</th>
            <th>city_1</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>23</td>
            <td>d5cb896d-3e99-4e19-9693-5e06ce987e53</td>
            <td>127 Brewing</td>
            <td>micro</td>
            <td>3090 Shirley Dr</td>
            <td>None</td>
            <td>None</td>
            <td>Jackson</td>
            <td>Michigan</td>
            <td>49201-7010</td>
            <td>United States</td>
            <td>5172581346</td>
            <td>None</td>
            <td>-84.43116792</td>
            <td>42.28667212</td>
            <td>0</td>
            <td>127 Brewing</td>
            <td>Jackson</td>
        </tr>
        <tr>
            <td>433</td>
            <td>ce507918-f969-4d44-a218-daca121c4bd0</td>
            <td>Ascension Brewing Company</td>
            <td>brewpub</td>
            <td>42000 Grand River Ave</td>
            <td>None</td>
            <td>None</td>
            <td>Novi</td>
            <td>Michigan</td>
            <td>48375-1831</td>
            <td>United States</td>
            <td>2483082093</td>
            <td><a href=http://www.ascension.beer>http://www.ascension.beer</a></td>
            <td>-83.4609783</td>
            <td>42.4783271</td>
            <td>5</td>
            <td>Ascension Brewing Co</td>
            <td>Novi</td>
        </tr>
        <tr>
            <td>467</td>
            <td>70fc7667-dda2-4cf0-ae7e-cbb43b5c43c4</td>
            <td>Atwater Brewing Co</td>
            <td>regional</td>
            <td>237 Joseph Campau St</td>
            <td>None</td>
            <td>None</td>
            <td>Detroit</td>
            <td>Michigan</td>
            <td>48207-4107</td>
            <td>United States</td>
            <td>3138779205</td>
            <td><a href=http://www.atwaterbeer.com>http://www.atwaterbeer.com</a></td>
            <td>-83.072904</td>
            <td>42.4190883</td>
            <td>6</td>
            <td>Atwater Brewery</td>
            <td>Detroit</td>
        </tr>
        <tr>
            <td>468</td>
            <td>14693124-55bc-43e9-817e-b99a85f662ac</td>
            <td>Atwater Grand Rapids</td>
            <td>brewpub</td>
            <td>201 Michigan St NW</td>
            <td>None</td>
            <td>None</td>
            <td>Grand Rapids</td>
            <td>Michigan</td>
            <td>49503-2323</td>
            <td>United States</td>
            <td>6166493020</td>
            <td>None</td>
            <td>-85.672454</td>
            <td>42.970463</td>
            <td>8</td>
            <td>Atwater Brewery</td>
            <td>Grand Rapids</td>
        </tr>
        <tr>
            <td>469</td>
            <td>89d36a6d-1226-4c61-b4c6-22676a55a364</td>
            <td>Atwater In the Park Biergarten and Tap House</td>
            <td>brewpub</td>
            <td>1175 Lakepointe St</td>
            <td>None</td>
            <td>None</td>
            <td>Grosse Pointe Park</td>
            <td>Michigan</td>
            <td>48230-1319</td>
            <td>United States</td>
            <td>3133445104</td>
            <td><a href=http://www.atwaterbeer.com>http://www.atwaterbeer.com</a></td>
            <td>-82.9399103</td>
            <td>42.3814617</td>
            <td>7</td>
            <td>Atwater in the Park</td>
            <td>Grosse Pointe Park</td>
        </tr>
        <tr>
            <td>557</td>
            <td>a79c77fa-dd4c-4e3f-bf34-c50ae8032f5c</td>
            <td>Baffin Brewing Co</td>
            <td>micro</td>
            <td>25113 Jefferson Ave</td>
            <td>None</td>
            <td>None</td>
            <td>Saint Clair Shores</td>
            <td>Michigan</td>
            <td>48081-2303</td>
            <td>United States</td>
            <td>5862187990</td>
            <td><a href=http://baffinbrewing.com>http://baffinbrewing.com</a></td>
            <td>-82.888964</td>
            <td>42.472227</td>
            <td>9</td>
            <td>Baffin Brewing Co</td>
            <td>Saint Clair Shores</td>
        </tr>
        <tr>
            <td>638</td>
            <td>545b51b8-caf4-4c63-aea4-48910ddef4c7</td>
            <td>Barn Brewers</td>
            <td>brewpub</td>
            <td>114 N Main St</td>
            <td>None</td>
            <td>None</td>
            <td>Lawton</td>
            <td>Michigan</td>
            <td>49065-8634</td>
            <td>United States</td>
            <td>2692990482</td>
            <td><a href=http://www.barnbrewersbrewery.com>http://www.barnbrewersbrewery.com</a></td>
            <td>-85.84966443</td>
            <td>42.16834814</td>
            <td>10</td>
            <td>Barn Brewery Brewers</td>
            <td>Lawton</td>
        </tr>
        <tr>
            <td>651</td>
            <td>4fc60749-c0d0-4f94-ab92-e5bf566d455e</td>
            <td>Barrel and Beam</td>
            <td>micro</td>
            <td>260 Northwoods RD</td>
            <td>None</td>
            <td>None</td>
            <td>Marquette</td>
            <td>Michigan</td>
            <td>49855</td>
            <td>United States</td>
            <td>9062732559</td>
            <td><a href=http://www.barrelandbeam.com>http://www.barrelandbeam.com</a></td>
            <td>-87.4722168</td>
            <td>46.54578</td>
            <td>11</td>
            <td>Barrel + Beam</td>
            <td>Marquette</td>
        </tr>
        <tr>
            <td>684</td>
            <td>e9ce69e2-13cf-48bd-81e2-10235db692aa</td>
            <td>Batch Brewing Co</td>
            <td>brewpub</td>
            <td>1400 Porter St</td>
            <td>None</td>
            <td>None</td>
            <td>Detroit</td>
            <td>Michigan</td>
            <td>48216-1934</td>
            <td>United States</td>
            <td>3133388008</td>
            <td><a href=http://www.BatchBrewingCompany.com>http://www.BatchBrewingCompany.com</a></td>
            <td>-83.088689</td>
            <td>42.3184806</td>
            <td>12</td>
            <td>Batch Brewing Co</td>
            <td>Detroit</td>
        </tr>
        <tr>
            <td>847</td>
            <td>71ed116d-2a0c-405f-9595-66b00f37030d</td>
            <td>Biere De Mac Brew Works</td>
            <td>micro</td>
            <td>14277 N Mackinaw Hwy</td>
            <td>None</td>
            <td>None</td>
            <td>Mackinaw City</td>
            <td>Michigan</td>
            <td>49701-8622</td>
            <td>United States</td>
            <td>2314277007</td>
            <td><a href=http://www.bieredemac.com>http://www.bieredemac.com</a></td>
            <td>None</td>
            <td>None</td>
            <td>14</td>
            <td>Biere De Mac Brew Works</td>
            <td>Mackinaw City</td>
        </tr>
        <tr>
            <td>1050</td>
            <td>7e39a579-8605-49a5-9988-a4d594650d1e</td>
            <td>Block Brewing Company</td>
            <td>brewpub</td>
            <td>1140 S Michigan Ave</td>
            <td>None</td>
            <td>None</td>
            <td>Howell</td>
            <td>Michigan</td>
            <td>48843-2669</td>
            <td>United States</td>
            <td>None</td>
            <td><a href=http://www.blockbrewingcompany.com>http://www.blockbrewingcompany.com</a></td>
            <td>-83.931806</td>
            <td>42.603858</td>
            <td>15</td>
            <td>Block Brewing Co</td>
            <td>Howell</td>
        </tr>
        <tr>
            <td>1220</td>
            <td>2de96915-c8c4-478d-ad67-0c8aa5828ab4</td>
            <td>Brass Ring Brewery</td>
            <td>brewpub</td>
            <td>2404 Eastern Ave SE</td>
            <td>None</td>
            <td>None</td>
            <td>Grand Rapids</td>
            <td>Michigan</td>
            <td>49507-3632</td>
            <td>United States</td>
            <td>6164601587</td>
            <td><a href=http://www.brassringbrewing.com>http://www.brassringbrewing.com</a></td>
            <td>-85.64741488</td>
            <td>42.91987422</td>
            <td>17</td>
            <td>Brass Ring Brewing</td>
            <td>Grand Rapids</td>
        </tr>
        <tr>
            <td>1293</td>
            <td>783c1657-bb0f-4718-87eb-676a8094d167</td>
            <td>Brewery Becker</td>
            <td>brewpub</td>
            <td>500 W Main St</td>
            <td>None</td>
            <td>None</td>
            <td>Brighton</td>
            <td>Michigan</td>
            <td>48116-1471</td>
            <td>United States</td>
            <td>8108440225</td>
            <td><a href=http://www.brauereibecker.us>http://www.brauereibecker.us</a></td>
            <td>-83.785481</td>
            <td>42.529522</td>
            <td>18</td>
            <td>Brewery Becker</td>
            <td>Brighton</td>
        </tr>
        <tr>
            <td>1298</td>
            <td>e0bf1313-ecf0-45c9-8b70-8ed121ec6a27</td>
            <td>Brewery Faisan</td>
            <td>planning</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
            <td>Detroit</td>
            <td>Michigan</td>
            <td>48207</td>
            <td>United States</td>
            <td>5867032030</td>
            <td><a href=http://www.breweryfaisan.com>http://www.breweryfaisan.com</a></td>
            <td>None</td>
            <td>None</td>
            <td>19</td>
            <td>Brewery Faison</td>
            <td>Detroit</td>
        </tr>
        <tr>
            <td>1299</td>
            <td>189be7db-796b-40db-87bf-daac88a4fac7</td>
            <td>Brewery Ferment</td>
            <td>micro</td>
            <td>511 S Union St</td>
            <td>None</td>
            <td>None</td>
            <td>Traverse City</td>
            <td>Michigan</td>
            <td>49684-3246</td>
            <td>United States</td>
            <td>2317358113</td>
            <td><a href=http://www.breweryferment.com>http://www.breweryferment.com</a></td>
            <td>-85.62353552</td>
            <td>44.75888715</td>
            <td>20</td>
            <td>Brewery Terra Firma</td>
            <td>Traverse City</td>
        </tr>
        <tr>
            <td>1305</td>
            <td>b6de3e97-fdec-4ac0-924d-5775fa7006bf</td>
            <td>Brewery Terra Firma</td>
            <td>micro</td>
            <td>2959 Hartman Rd</td>
            <td>None</td>
            <td>None</td>
            <td>Traverse City</td>
            <td>Michigan</td>
            <td>49685-8838</td>
            <td>United States</td>
            <td>2319291600</td>
            <td><a href=http://www.breweryterrafirma.com>http://www.breweryterrafirma.com</a></td>
            <td>-85.63510581</td>
            <td>44.71509875</td>
            <td>20</td>
            <td>Brewery Terra Firma</td>
            <td>Traverse City</td>
        </tr>
        <tr>
            <td>1375</td>
            <td>0b3fde08-24cd-4523-b824-cf558568a608</td>
            <td>Brite Eyes Brewing Co</td>
            <td>brewpub</td>
            <td>1156 S Burdick St</td>
            <td>None</td>
            <td>None</td>
            <td>Kalamazoo</td>
            <td>Michigan</td>
            <td>49001-2741</td>
            <td>United States</td>
            <td>2692205001</td>
            <td>None</td>
            <td>-85.582846</td>
            <td>42.27977268</td>
            <td>21</td>
            <td>Brite Eye Brewing Co</td>
            <td>Kalamazoo</td>
        </tr>
        <tr>
            <td>1738</td>
            <td>b7f56781-add6-4bba-a3df-62a254f64971</td>
            <td>Cheboygan Brewing Co</td>
            <td>micro</td>
            <td>101 N Main St</td>
            <td>None</td>
            <td>None</td>
            <td>Cheboygan</td>
            <td>Michigan</td>
            <td>49721-1637</td>
            <td>United States</td>
            <td>2312683277</td>
            <td><a href=http://www.cheboyganbrewing.com>http://www.cheboyganbrewing.com</a></td>
            <td>-84.4743633</td>
            <td>45.647403</td>
            <td>23</td>
            <td>Cheboygan Brewing Co</td>
            <td>Cheboygan</td>
        </tr>
        <tr>
            <td>1791</td>
            <td>6812d6f9-b113-48c1-902a-dd2b0f1e5874</td>
            <td>City Built Brewing Company</td>
            <td>brewpub</td>
            <td>820 Monroe Ave NW Ste 155</td>
            <td>None</td>
            <td>None</td>
            <td>Grand Rapids</td>
            <td>Michigan</td>
            <td>49503-1413</td>
            <td>United States</td>
            <td>6168055755</td>
            <td><a href=http://www.citybuiltbrewing.com>http://www.citybuiltbrewing.com</a></td>
            <td>None</td>
            <td>None</td>
            <td>24</td>
            <td>City Built Brewing Co</td>
            <td>Grand Rapids</td>
        </tr>
        <tr>
            <td>2129</td>
            <td>70dbd577-56bb-4480-b51c-6f33123147d3</td>
            <td>Dark Horse Brewing Co</td>
            <td>micro</td>
            <td>511 S Kalamazoo Ave</td>
            <td>None</td>
            <td>None</td>
            <td>Marshall</td>
            <td>Michigan</td>
            <td>49068-1718</td>
            <td>United States</td>
            <td>2697819940</td>
            <td><a href=http://www.darkhorsebrewery.com>http://www.darkhorsebrewery.com</a></td>
            <td>-84.96397652</td>
            <td>42.26638824</td>
            <td>27</td>
            <td>Dark Horse Brewing Co</td>
            <td>Marshall</td>
        </tr>
        <tr>
            <td>2161</td>
            <td>1d6ae265-d8dd-4a19-9524-801019b356c6</td>
            <td>Dearborn Brewing</td>
            <td>micro</td>
            <td>21930 Michigan Ave</td>
            <td>None</td>
            <td>None</td>
            <td>Dearborn</td>
            <td>Michigan</td>
            <td>48124-2351</td>
            <td>United States</td>
            <td>3139144187</td>
            <td><a href=http://www.dearbornbrewing.com>http://www.dearbornbrewing.com</a></td>
            <td>-83.2433835</td>
            <td>42.30664415</td>
            <td>28</td>
            <td>Dearborn Brewing Co</td>
            <td>Dearborn</td>
        </tr>
        <tr>
            <td>2263</td>
            <td>fd4412ec-311a-4e87-8b55-10dca2b00b94</td>
            <td>Dimes Brewhouse</td>
            <td>micro</td>
            <td>145 N Bridge St</td>
            <td>None</td>
            <td>None</td>
            <td>Dimondale</td>
            <td>Michigan</td>
            <td>48821-9201</td>
            <td>United States</td>
            <td>5173032067</td>
            <td><a href=http://www.dimesbrewhouse.com>http://www.dimesbrewhouse.com</a></td>
            <td>-84.6483014</td>
            <td>42.64660055</td>
            <td>29</td>
            <td>Dimes Brewhouse</td>
            <td>Dimondale</td>
        </tr>
        <tr>
            <td>2344</td>
            <td>51421b18-93e4-4d09-b694-388dcb5df2fe</td>
            <td>Downey Brewing Company</td>
            <td>micro</td>
            <td>13121 Prospect St</td>
            <td>None</td>
            <td>None</td>
            <td>Dearborn</td>
            <td>Michigan</td>
            <td>48126-3603</td>
            <td>United States</td>
            <td>3132888442</td>
            <td><a href=http://www.downeybrewingcompany.com>http://www.downeybrewingcompany.com</a></td>
            <td>-83.17350725</td>
            <td>42.31625295</td>
            <td>31</td>
            <td>Downey Brewing Co</td>
            <td>Dearborn</td>
        </tr>
        <tr>
            <td>2465</td>
            <td>497485a3-1317-450d-894b-0c079b1668b6</td>
            <td>EB Coffee and Pub</td>
            <td>brewpub</td>
            <td>8980 N Rodgers Ct SE</td>
            <td>None</td>
            <td>None</td>
            <td>Caledonia</td>
            <td>Michigan</td>
            <td>49316-8051</td>
            <td>United States</td>
            <td>6168917700</td>
            <td><a href=http://www.ebcoffeepub.com>http://www.ebcoffeepub.com</a></td>
            <td>-85.50839333</td>
            <td>42.80145682</td>
            <td>37</td>
            <td>EB Coffee & Pub</td>
            <td>Caledonia</td>
        </tr>
        <tr>
            <td>2438</td>
            <td>1996e0fa-b821-4d44-a6ea-d5fda91c7d47</td>
            <td>EagleMonk Pub and Brewery</td>
            <td>brewpub</td>
            <td>4906 W Mount Hope Hwy</td>
            <td>None</td>
            <td>None</td>
            <td>Lansing</td>
            <td>Michigan</td>
            <td>48917-9588</td>
            <td>United States</td>
            <td>5177087350</td>
            <td>None</td>
            <td>-84.662048</td>
            <td>42.712054</td>
            <td>34</td>
            <td>EagleMonk Pub & Brewery</td>
            <td>Lansing</td>
        </tr>
        <tr>
            <td>2446</td>
            <td>21a0d567-e70c-49fa-bb93-a073ed29e93b</td>
            <td>Earthen Ales</td>
            <td>micro</td>
            <td>1371 Gray Dr Ste 200</td>
            <td>None</td>
            <td>None</td>
            <td>Traverse City</td>
            <td>Michigan</td>
            <td>49684-7805</td>
            <td>United States</td>
            <td>2312524270</td>
            <td><a href=http://www.earthenales.com>http://www.earthenales.com</a></td>
            <td>-85.6454249</td>
            <td>44.755294</td>
            <td>35</td>
            <td>Earthen Ales</td>
            <td>Traverse City</td>
        </tr>
        <tr>
            <td>2459</td>
            <td>9ef70c89-a7e9-4759-a71e-4adca64c543a</td>
            <td>Eastern Market Brewing Company</td>
            <td>micro</td>
            <td>2515 Riopelle St</td>
            <td>None</td>
            <td>None</td>
            <td>Detroit</td>
            <td>Michigan</td>
            <td>48207-4526</td>
            <td>United States</td>
            <td>None</td>
            <td><a href=http://www.easternmarket.beer>http://www.easternmarket.beer</a></td>
            <td>-83.03876229</td>
            <td>42.346625</td>
            <td>36</td>
            <td>Eastern Market Brewing Co</td>
            <td>Detroit</td>
        </tr>
        <tr>
            <td>2522</td>
            <td>33420b64-b564-4d19-b134-364a4721e50f</td>
            <td>Elk Street Brewery</td>
            <td>brewpub</td>
            <td>3 S Elk St</td>
            <td>None</td>
            <td>None</td>
            <td>Sandusky</td>
            <td>Michigan</td>
            <td>48471-1353</td>
            <td>United States</td>
            <td>8103008049</td>
            <td><a href=http://www.elkstreetbrewery.com>http://www.elkstreetbrewery.com</a></td>
            <td>-82.82977088</td>
            <td>43.42036406</td>
            <td>38</td>
            <td>Elk Street Brewery</td>
            <td>Sandusky</td>
        </tr>
        <tr>
            <td>2591</td>
            <td>b8b99a39-f008-4bde-9824-c5aaf4ba6b61</td>
            <td>Eternity Brewing Co</td>
            <td>micro</td>
            <td>4060 E Grand River Ave</td>
            <td>None</td>
            <td>None</td>
            <td>Howell</td>
            <td>Michigan</td>
            <td>48843-8583</td>
            <td>United States</td>
            <td>5172954904</td>
            <td><a href=http://www.eternitybrewing.com>http://www.eternitybrewing.com</a></td>
            <td>-83.9948055</td>
            <td>42.63273017</td>
            <td>40</td>
            <td>Eternity Brewing Co</td>
            <td>Howell</td>
        </tr>
        <tr>
            <td>2654</td>
            <td>b2a43962-9a78-4e6d-882c-d3cb13129327</td>
            <td>Farmington Brewing Company LLC</td>
            <td>micro</td>
            <td>33336 Grand River Ave</td>
            <td>None</td>
            <td>None</td>
            <td>Farmington</td>
            <td>Michigan</td>
            <td>48336-3124</td>
            <td>United States</td>
            <td>2489579543</td>
            <td><a href=http://fbcbrewing.com>http://fbcbrewing.com</a></td>
            <td>-83.37603742</td>
            <td>42.4649541</td>
            <td>41</td>
            <td>Farmington Brewing Co</td>
            <td>Farmington</td>
        </tr>
        <tr>
            <td>2655</td>
            <td>6cbd8d7c-b5af-4eda-802c-550643b62d42</td>
            <td>Farmington Underground Canning and Kegging</td>
            <td>micro</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
            <td>Farmington</td>
            <td>Michigan</td>
            <td>48336</td>
            <td>United States</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
            <td>41</td>
            <td>Farmington Brewing Co</td>
            <td>Farmington</td>
        </tr>
        <tr>
            <td>2689</td>
            <td>2a824990-9f77-4b8a-ae7b-76aba05c5504</td>
            <td>Fenton Winery & Brewery</td>
            <td>brewpub</td>
            <td>1370 N Long Lake Rd</td>
            <td>None</td>
            <td>None</td>
            <td>Fenton</td>
            <td>Michigan</td>
            <td>48430-8867</td>
            <td>United States</td>
            <td>8103734194</td>
            <td><a href=http://www.fentonwinery.com>http://www.fentonwinery.com</a></td>
            <td>-83.70470876</td>
            <td>42.85155557</td>
            <td>42</td>
            <td>Fenton Winery & Brewery</td>
            <td>Fenton</td>
        </tr>
        <tr>
            <td>3313</td>
            <td>aca6e2d8-e3b8-4a29-b77b-f5d91b3179dc</td>
            <td>Greenbush Annex</td>
            <td>micro</td>
            <td>5870 Sawyer Rd</td>
            <td>None</td>
            <td>None</td>
            <td>Sawyer</td>
            <td>Michigan</td>
            <td>49125</td>
            <td>United States</td>
            <td>None</td>
            <td>None</td>
            <td>-86.59404729</td>
            <td>41.88540971</td>
            <td>49</td>
            <td>Greenbush Brewing Co</td>
            <td>Sawyer</td>
        </tr>
        <tr>
            <td>3314</td>
            <td>295e2956-574d-4cf1-a472-2df77d81bbc3</td>
            <td>Greenbush Brewing Co</td>
            <td>micro</td>
            <td>5885 Sawyer Rd</td>
            <td>None</td>
            <td>None</td>
            <td>Sawyer</td>
            <td>Michigan</td>
            <td>49125-9380</td>
            <td>United States</td>
            <td>2694051076</td>
            <td><a href=http://greenbushbrewing.com>http://greenbushbrewing.com</a></td>
            <td>-86.59431314</td>
            <td>41.88557686</td>
            <td>49</td>
            <td>Greenbush Brewing Co</td>
            <td>Sawyer</td>
        </tr>
        <tr>
            <td>3349</td>
            <td>91361172-9875-4337-8a10-e12765e47071</td>
            <td>Grizzly Peak Brewing Co</td>
            <td>brewpub</td>
            <td>120 W Washington St Ste 1</td>
            <td>None</td>
            <td>None</td>
            <td>Ann Arbor</td>
            <td>Michigan</td>
            <td>48104-1356</td>
            <td>United States</td>
            <td>7347417325</td>
            <td><a href=http://www.grizzlypeak.net>http://www.grizzlypeak.net</a></td>
            <td>None</td>
            <td>None</td>
            <td>50</td>
            <td>Grizzly Peak Brewing Co</td>
            <td>Ann Arbor</td>
        </tr>
        <tr>
            <td>3371</td>
            <td>c67959ff-a884-4d4a-be84-a194b410b969</td>
            <td>Guardian Brewing Company</td>
            <td>planning</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
            <td>Saugatuck</td>
            <td>Michigan</td>
            <td>49453</td>
            <td>United States</td>
            <td>8125213611</td>
            <td><a href=http://www.guardianbrewingco.com>http://www.guardianbrewingco.com</a></td>
            <td>None</td>
            <td>None</td>
            <td>51</td>
            <td>Guardian Brewing Co</td>
            <td>Saugatuck</td>
        </tr>
        <tr>
            <td>3636</td>
            <td>bf32b0e2-4258-4f4a-9b72-744d0582034c</td>
            <td>HOMES Brewery</td>
            <td>brewpub</td>
            <td>2321 Jackson Ave</td>
            <td>None</td>
            <td>None</td>
            <td>Ann Arbor</td>
            <td>Michigan</td>
            <td>48103-3814</td>
            <td>United States</td>
            <td>7349546637</td>
            <td><a href=http://www.homesbrewery.com>http://www.homesbrewery.com</a></td>
            <td>-83.77845211</td>
            <td>42.28032635</td>
            <td>55</td>
            <td>HOMES Brewery</td>
            <td>Ann Arbor</td>
        </tr>
        <tr>
            <td>3450</td>
            <td>063bdb40-056e-489a-b952-0746b4b9e512</td>
            <td>Harmony Brewing Co</td>
            <td>brewpub</td>
            <td>1551 Lake Dr SE</td>
            <td>None</td>
            <td>None</td>
            <td>Grand Rapids</td>
            <td>Michigan</td>
            <td>49506-2705</td>
            <td>United States</td>
            <td>6162330063</td>
            <td>None</td>
            <td>-85.63066846</td>
            <td>42.95501346</td>
            <td>52</td>
            <td>Harmony Brewing Co</td>
            <td>Grand Rapids</td>
        </tr>
        <tr>
            <td>3451</td>
            <td>a3bd0375-cc0f-48cf-82f9-48151f0c411b</td>
            <td>Harmony Brewing Company   Harmony Hall</td>
            <td>brewpub</td>
            <td>401 Stocking Ave NW</td>
            <td>None</td>
            <td>None</td>
            <td>Grand Rapids</td>
            <td>Michigan</td>
            <td>49504-5501</td>
            <td>United States</td>
            <td>None</td>
            <td>None</td>
            <td>-85.684118</td>
            <td>42.970732</td>
            <td>52</td>
            <td>Harmony Brewing Co</td>
            <td>Grand Rapids</td>
        </tr>
        <tr>
            <td>3471</td>
            <td>3b737ad4-2770-48e6-8897-644fa1886e7a</td>
            <td>Haymarket Brewery and Taproom</td>
            <td>micro</td>
            <td>9301 Red Arrow Hwy</td>
            <td>None</td>
            <td>None</td>
            <td>Bridgman</td>
            <td>Michigan</td>
            <td>49106-9558</td>
            <td>United States</td>
            <td>3126380707</td>
            <td>None</td>
            <td>-86.55947806</td>
            <td>41.94715363</td>
            <td>53</td>
            <td>Haymarket Taproom</td>
            <td>Bridgman</td>
        </tr>
        <tr>
            <td>3633</td>
            <td>3bc09d93-e165-4c5a-81ee-9228b5e3d54d</td>
            <td>HomeGrown Brewing Company</td>
            <td>brewpub</td>
            <td>28 N Washington St</td>
            <td>None</td>
            <td>None</td>
            <td>Oxford</td>
            <td>Michigan</td>
            <td>48371-4665</td>
            <td>United States</td>
            <td>2488918290</td>
            <td><a href=http://www.homegrownbrewco.com>http://www.homegrownbrewco.com</a></td>
            <td>-83.26650386</td>
            <td>42.82656071</td>
            <td>54</td>
            <td>HomeGrown Brewing Co</td>
            <td>Oxford</td>
        </tr>
        <tr>
            <td>3947</td>
            <td>9c26b2c6-1017-4c43-876b-17f7a0d1afa5</td>
            <td>Jamesport Brewing Co</td>
            <td>brewpub</td>
            <td>410 S James St</td>
            <td>None</td>
            <td>None</td>
            <td>Ludington</td>
            <td>Michigan</td>
            <td>49431-2159</td>
            <td>United States</td>
            <td>2318452522</td>
            <td><a href=http://www.jamesportbrewingcompany.com>http://www.jamesportbrewingcompany.com</a></td>
            <td>-86.44787302</td>
            <td>43.95168567</td>
            <td>56</td>
            <td>Jamesport Brewing Co</td>
            <td>Ludington</td>
        </tr>
        <tr>
            <td>3950</td>
            <td>f5951169-a180-4970-aa22-28c534adf3a5</td>
            <td>Jamex Brewing Co</td>
            <td>micro</td>
            <td>21721 Harper Ave</td>
            <td>None</td>
            <td>None</td>
            <td>Saint Clair Shores</td>
            <td>Michigan</td>
            <td>48080-2213</td>
            <td>United States</td>
            <td>5869442030</td>
            <td><a href=http://www.jamexbrewing.com>http://www.jamexbrewing.com</a></td>
            <td>-82.8986171</td>
            <td>42.5171113</td>
            <td>57</td>
            <td>Jamex Brewing Co</td>
            <td>Saint Clair Shores</td>
        </tr>
        <tr>
            <td>4174</td>
            <td>8a240e31-0084-4199-9c3b-ae9b40785cfa</td>
            <td>Lake Ann Brewing Co</td>
            <td>micro</td>
            <td>6535 First St</td>
            <td>None</td>
            <td>None</td>
            <td>Lake Ann</td>
            <td>Michigan</td>
            <td>49650-9549</td>
            <td>United States</td>
            <td>2316402327</td>
            <td><a href=http://www.lakeannbrewing.com>http://www.lakeannbrewing.com</a></td>
            <td>None</td>
            <td>None</td>
            <td>59</td>
            <td>Lake Ann Brewing Co</td>
            <td>Lake Ann</td>
        </tr>
        <tr>
            <td>4295</td>
            <td>e8496c18-2615-4e81-a29f-ea64ea88269c</td>
            <td>Les Cheneaux Distillers</td>
            <td>brewpub</td>
            <td>172 S Meridian St</td>
            <td>None</td>
            <td>None</td>
            <td>Cedarville</td>
            <td>Michigan</td>
            <td>49719</td>
            <td>United States</td>
            <td>9064841213</td>
            <td><a href=http://www.lescheneauxdistillers.com>http://www.lescheneauxdistillers.com</a></td>
            <td>-84.36301089</td>
            <td>45.99742724</td>
            <td>60</td>
            <td>Les Cheneaux Distillers</td>
            <td>Cedarville</td>
        </tr>
        <tr>
            <td>4511</td>
            <td>1d395c71-54a6-4af8-a0a3-21f5946f49ee</td>
            <td>Ludington Bay Brewing Co</td>
            <td>brewpub</td>
            <td>515 S James St</td>
            <td>None</td>
            <td>None</td>
            <td>Ludington</td>
            <td>Michigan</td>
            <td>49431-2325</td>
            <td>United States</td>
            <td>2312396690</td>
            <td><a href=http://ludingtonbaybrewing.com>http://ludingtonbaybrewing.com</a></td>
            <td>-86.44763415</td>
            <td>43.95070214</td>
            <td>63</td>
            <td>Ludington Bay Brewing Co</td>
            <td>Ludington</td>
        </tr>
        <tr>
            <td>4765</td>
            <td>b43a8593-0e7e-42dd-b3f1-0c1ec9da44bf</td>
            <td>Michigan Brewing Works</td>
            <td>brewpub</td>
            <td>3435 Dietz Rd</td>
            <td>None</td>
            <td>None</td>
            <td>Williamston</td>
            <td>Michigan</td>
            <td>48895-9506</td>
            <td>United States</td>
            <td>5176557777</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
            <td>65</td>
            <td>Michigan Brew Works & Garlic Farm</td>
            <td>Williamston</td>
        </tr>
        <tr>
            <td>4773</td>
            <td>146870a5-aad1-4b3a-9b25-c4af2a711f1a</td>
            <td>Midland Brewing Co</td>
            <td>brewpub</td>
            <td>5011 N Saginaw Road</td>
            <td>None</td>
            <td>None</td>
            <td>Midland</td>
            <td>Michigan</td>
            <td>48642</td>
            <td>United States</td>
            <td>9892597210</td>
            <td><a href=http://www.midlandbrewing.com>http://www.midlandbrewing.com</a></td>
            <td>-84.2910925</td>
            <td>43.6408678</td>
            <td>68</td>
            <td>Midland Brewing Co</td>
            <td>Midland</td>
        </tr>
        <tr>
            <td>4943</td>
            <td>82718ba8-c506-467b-88e2-5f5b714a4999</td>
            <td>Motor City Brewing Works</td>
            <td>brewpub</td>
            <td>470 W Canfield St</td>
            <td>None</td>
            <td>None</td>
            <td>Detroit</td>
            <td>Michigan</td>
            <td>48201-1220</td>
            <td>United States</td>
            <td>3138322700</td>
            <td><a href=http://www.motorcitybeer.com>http://www.motorcitybeer.com</a></td>
            <td>-83.06603733</td>
            <td>42.3518156</td>
            <td>69</td>
            <td>Motor City Brewing Works</td>
            <td>Detroit</td>
        </tr>
        <tr>
            <td>4944</td>
            <td>9a7bc77f-ac7d-4ed8-bb95-3f374523a01f</td>
            <td>Motor City Brewing Works Warehouse</td>
            <td>micro</td>
            <td>441 W Canfield St</td>
            <td>None</td>
            <td>None</td>
            <td>Detroit</td>
            <td>Michigan</td>
            <td>48201-1231</td>
            <td>United States</td>
            <td>None</td>
            <td>None</td>
            <td>-83.06474893</td>
            <td>42.35151997</td>
            <td>69</td>
            <td>Motor City Brewing Works</td>
            <td>Detroit</td>
        </tr>
        <tr>
            <td>5145</td>
            <td>2fe4a822-2435-4f5c-bebf-66a40909e8d4</td>
            <td>North Center Brewing Co</td>
            <td>brewpub</td>
            <td>410 N Center St</td>
            <td>None</td>
            <td>None</td>
            <td>Northville</td>
            <td>Michigan</td>
            <td>48167-1224</td>
            <td>United States</td>
            <td>2484447967</td>
            <td><a href=http://www.northcenterbrewing.com>http://www.northcenterbrewing.com</a></td>
            <td>-83.48311135</td>
            <td>42.43412107</td>
            <td>72</td>
            <td>North Center Brewing Co</td>
            <td>Northville</td>
        </tr>
        <tr>
            <td>5146</td>
            <td>2983edd7-074e-4a0e-970f-6fc938c07e94</td>
            <td>North Channel Brewing Co</td>
            <td>brewpub</td>
            <td>86 Washington St</td>
            <td>None</td>
            <td>None</td>
            <td>Manistee</td>
            <td>Michigan</td>
            <td>49660-1231</td>
            <td>United States</td>
            <td>2312991020</td>
            <td><a href=http://www.NorthChannelBrewing.com>http://www.NorthChannelBrewing.com</a></td>
            <td>-86.32379888</td>
            <td>44.24929388</td>
            <td>73</td>
            <td>North Channel Brewing</td>
            <td>Manistee</td>
        </tr>
        <tr>
            <td>5162</td>
            <td>8e765472-33c8-4e1d-8923-20973d75e6f4</td>
            <td>North Pier Brewing</td>
            <td>micro</td>
            <td>670 N Shore Dr</td>
            <td>None</td>
            <td>None</td>
            <td>Benton Harbor</td>
            <td>Michigan</td>
            <td>49022-3646</td>
            <td>United States</td>
            <td>2697577163</td>
            <td><a href=http://www.northpierbrewing.com>http://www.northpierbrewing.com</a></td>
            <td>-86.45499315</td>
            <td>42.12454349</td>
            <td>75</td>
            <td>North Pier Brewing Co</td>
            <td>Benton Harbor</td>
        </tr>
        <tr>
            <td>5176</td>
            <td>e19854be-9a28-4587-b055-90e13fcbd7fb</td>
            <td>Northville Winery and Brewery</td>
            <td>micro</td>
            <td>630 Old Baseline Rd</td>
            <td>None</td>
            <td>None</td>
            <td>Northville</td>
            <td>Michigan</td>
            <td>48167</td>
            <td>United States</td>
            <td>2483206507</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
            <td>76</td>
            <td>Northville Winery and Brewing Co</td>
            <td>Northville</td>
        </tr>
        <tr>
            <td>5396</td>
            <td>b778b875-6f29-41fc-94f9-23c09e872530</td>
            <td>Ore Dock Brewing Co</td>
            <td>micro</td>
            <td>114 W Spring St</td>
            <td>None</td>
            <td>None</td>
            <td>Marquette</td>
            <td>Michigan</td>
            <td>49855-4608</td>
            <td>United States</td>
            <td>9068675309</td>
            <td><a href=http://www.oredockbrewingcompany.com>http://www.oredockbrewingcompany.com</a></td>
            <td>-87.39388593</td>
            <td>46.54189135</td>
            <td>78</td>
            <td>Ore Dock Brewing Co</td>
            <td>Marquette</td>
        </tr>
        <tr>
            <td>5401</td>
            <td>39db40da-0ee5-465d-9a9a-38f9eb51ba09</td>
            <td>Original Gravity Brewing Co</td>
            <td>brewpub</td>
            <td>440 County St</td>
            <td>None</td>
            <td>None</td>
            <td>Milan</td>
            <td>Michigan</td>
            <td>48160-1504</td>
            <td>United States</td>
            <td>7344397490</td>
            <td><a href=http://www.ogbrewing.com>http://www.ogbrewing.com</a></td>
            <td>-83.67523597</td>
            <td>42.083974</td>
            <td>79</td>
            <td>Original Gravity Brewing Co</td>
            <td>Milan</td>
        </tr>
        <tr>
            <td>5474</td>
            <td>6ea02eb4-7386-46bf-ac13-fc452dabc976</td>
            <td>Paddle Hard Brewing</td>
            <td>brewpub</td>
            <td>227 E Michigan Ave</td>
            <td>None</td>
            <td>None</td>
            <td>Grayling</td>
            <td>Michigan</td>
            <td>49738-1742</td>
            <td>United States</td>
            <td>9897456388</td>
            <td>None</td>
            <td>-84.71675164</td>
            <td>44.6600473</td>
            <td>80</td>
            <td>Paddle Hard Brewing</td>
            <td>Grayling</td>
        </tr>
        <tr>
            <td>5473</td>
            <td>4c9cdd7b-ea79-4d81-b629-fbfdf899dce6</td>
            <td>Paddle Hard Brewing</td>
            <td>micro</td>
            <td>118 Michigan Ave</td>
            <td>None</td>
            <td>None</td>
            <td>Grayling</td>
            <td>Michigan</td>
            <td>49738-1741</td>
            <td>United States</td>
            <td>9897456388</td>
            <td><a href=http://paddlehardbrewing.com>http://paddlehardbrewing.com</a></td>
            <td>-84.709584</td>
            <td>44.66521</td>
            <td>80</td>
            <td>Paddle Hard Brewing</td>
            <td>Grayling</td>
        </tr>
        <tr>
            <td>5590</td>
            <td>067530c6-4848-4418-9a45-92dd720cc681</td>
            <td>Petoskey Brewing</td>
            <td>micro</td>
            <td>1844 M 119 Harbor-Petoskey Rd</td>
            <td>None</td>
            <td>None</td>
            <td>Petoskey</td>
            <td>Michigan</td>
            <td>49770-9341</td>
            <td>United States</td>
            <td>2317532057</td>
            <td><a href=http://www.petoskeybrewing.com>http://www.petoskeybrewing.com</a></td>
            <td>None</td>
            <td>None</td>
            <td>81</td>
            <td>Petoskey Brewing Co</td>
            <td>Petoskey</td>
        </tr>
        <tr>
            <td>5613</td>
            <td>88293b57-bdc4-4fb7-afb4-1de24a9e82e9</td>
            <td>Pigeon Hill Brewery Production Facility</td>
            <td>planning</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
            <td>Muskegon</td>
            <td>Michigan</td>
            <td>49440-1109</td>
            <td>United States</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
            <td>82</td>
            <td>Pigeon Hill Brewing Co</td>
            <td>Muskegon</td>
        </tr>
        <tr>
            <td>5614</td>
            <td>c7296318-3def-485e-9f58-b76d2db04b25</td>
            <td>Pigeon Hill Brewing Co</td>
            <td>micro</td>
            <td>500 W Western Ave Ste 100</td>
            <td>None</td>
            <td>None</td>
            <td>Muskegon</td>
            <td>Michigan</td>
            <td>49440-1000</td>
            <td>United States</td>
            <td>2313755184</td>
            <td><a href=http://www.pigeonhillbrew.com>http://www.pigeonhillbrew.com</a></td>
            <td>None</td>
            <td>None</td>
            <td>82</td>
            <td>Pigeon Hill Brewing Co</td>
            <td>Muskegon</td>
        </tr>
        <tr>
            <td>5884</td>
            <td>305b584c-df57-4c6f-98d2-491736bbe7e9</td>
            <td>Rare Bird Brewpub</td>
            <td>brewpub</td>
            <td>229 Lake Ave</td>
            <td>None</td>
            <td>None</td>
            <td>Traverse City</td>
            <td>Michigan</td>
            <td>49684-2532</td>
            <td>United States</td>
            <td>2319432053</td>
            <td><a href=http://rarebirdbrewpub.com>http://rarebirdbrewpub.com</a></td>
            <td>-85.61932337</td>
            <td>44.75978039</td>
            <td>85</td>
            <td>Rare Bird Brewpub</td>
            <td>Traverse City</td>
        </tr>
        <tr>
            <td>5954</td>
            <td>396f0a00-1910-4667-a65d-79ce19556af7</td>
            <td>Redline Brewing Company</td>
            <td>micro</td>
            <td>5470 Lapeer Rd</td>
            <td>None</td>
            <td>None</td>
            <td>Burton</td>
            <td>Michigan</td>
            <td>48509-2235</td>
            <td>United States</td>
            <td>8102592009</td>
            <td><a href=http://redlinebrewingcompany.com>http://redlinebrewingcompany.com</a></td>
            <td>-83.59795244</td>
            <td>43.01100775</td>
            <td>86</td>
            <td>Redline Brewing Co</td>
            <td>Burton</td>
        </tr>
        <tr>
            <td>6087</td>
            <td>54dbff86-41fe-421c-937e-2903bac3c9f7</td>
            <td>Rochester Mills Production Brewery</td>
            <td>micro</td>
            <td>3275 Lapeer W Rd</td>
            <td>None</td>
            <td>None</td>
            <td>Auburn Hills</td>
            <td>Michigan</td>
            <td>48326-1723</td>
            <td>United States</td>
            <td>2483773130</td>
            <td><a href=http://www.beercos.com>http://www.beercos.com</a></td>
            <td>None</td>
            <td>None</td>
            <td>88</td>
            <td>Rochester Mills Production Brewery</td>
            <td>Auburn Hills</td>
        </tr>
        <tr>
            <td>6161</td>
            <td>bbc83a19-c5a5-44f1-bf6f-969f209cdfc0</td>
            <td>Rolling Oak Brewing Co</td>
            <td>micro</td>
            <td>509 Norway St</td>
            <td>None</td>
            <td>None</td>
            <td>Grayling</td>
            <td>Michigan</td>
            <td>49738-1719</td>
            <td>United States</td>
            <td>9897456280</td>
            <td><a href=http://rollingoakbrewing.com>http://rollingoakbrewing.com</a></td>
            <td>-84.716671</td>
            <td>44.661765</td>
            <td>89</td>
            <td>Rolling Oak Brewing Co</td>
            <td>Grayling</td>
        </tr>
        <tr>
            <td>6188</td>
            <td>0654ac67-ff7f-4b63-a876-bbd24a31fb57</td>
            <td>Round Barn Brewery & Public House</td>
            <td>micro</td>
            <td>9151 First St</td>
            <td>None</td>
            <td>None</td>
            <td>Baroda</td>
            <td>Michigan</td>
            <td>49101-8927</td>
            <td>United States</td>
            <td>2693267059</td>
            <td><a href=http://www.roundbarnbrewery.com>http://www.roundbarnbrewery.com</a></td>
            <td>None</td>
            <td>None</td>
            <td>90</td>
            <td>Round Barn Winery, Distillery, Brewery</td>
            <td>Baroda</td>
        </tr>
        <tr>
            <td>6187</td>
            <td>eb65fa0c-3e45-41cb-bcc3-de1a1060b67e</td>
            <td>Round Barn Brewery / Round Barn Winery</td>
            <td>micro</td>
            <td>10983 Hills Rd</td>
            <td>None</td>
            <td>None</td>
            <td>Baroda</td>
            <td>Michigan</td>
            <td>49101-8742</td>
            <td>United States</td>
            <td>8007169463</td>
            <td><a href=http://www.roundbarnwinery.com>http://www.roundbarnwinery.com</a></td>
            <td>-86.479395</td>
            <td>41.950161</td>
            <td>90</td>
            <td>Round Barn Winery, Distillery, Brewery</td>
            <td>Baroda</td>
        </tr>
        <tr>
            <td>6236</td>
            <td>5bc03c91-fce7-4e14-9aeb-05303d7e6bbb</td>
            <td>Rustic Leaf Brewing Company</td>
            <td>micro</td>
            <td>7200 Highland Rd</td>
            <td>None</td>
            <td>None</td>
            <td>Waterford</td>
            <td>Michigan</td>
            <td>48327-1506</td>
            <td>United States</td>
            <td>2485999933</td>
            <td><a href=http://www.rusticleafbrewingcompany.com>http://www.rusticleafbrewingcompany.com</a></td>
            <td>-83.4048664</td>
            <td>42.659901</td>
            <td>91</td>
            <td>Rustic Leaf Brewing Co</td>
            <td>Waterford</td>
        </tr>
        <tr>
            <td>6311</td>
            <td>75b2ab04-6d86-4928-8953-d60c4b1c172e</td>
            <td>Sandhill Crane Vineyards</td>
            <td>micro</td>
            <td>4724 Walz Rd</td>
            <td>None</td>
            <td>None</td>
            <td>Jackson</td>
            <td>Michigan</td>
            <td>49201-9613</td>
            <td>United States</td>
            <td>5177640679</td>
            <td><a href=http://www.sandhillcranevineyards.com>http://www.sandhillcranevineyards.com</a></td>
            <td>None</td>
            <td>None</td>
            <td>94</td>
            <td>Sandhill Crane Vineyards</td>
            <td>Jackson</td>
        </tr>
        <tr>
            <td>6515</td>
            <td>74f8802c-da96-40d7-8e37-3fd4dd2cb381</td>
            <td>Silver Harbor Brewing Company</td>
            <td>brewpub</td>
            <td>721 Pleasant St</td>
            <td>None</td>
            <td>None</td>
            <td>Saint Joseph</td>
            <td>Michigan</td>
            <td>49085-1241</td>
            <td>United States</td>
            <td>2692817100</td>
            <td><a href=http://silverharborbrewing.com>http://silverharborbrewing.com</a></td>
            <td>-86.48027459</td>
            <td>42.10893033</td>
            <td>99</td>
            <td>Silver Harbor Brewing Co</td>
            <td>Saint Joseph</td>
        </tr>
        <tr>
            <td>6621</td>
            <td>3467fc4d-c507-46c1-a962-31d470bb204c</td>
            <td>Snowbelt Brewing Company</td>
            <td>brewpub</td>
            <td>132 W Main St Unit 1</td>
            <td>None</td>
            <td>None</td>
            <td>Gaylord</td>
            <td>Michigan</td>
            <td>49735-2304</td>
            <td>United States</td>
            <td>9894487077</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
            <td>101</td>
            <td>Snowbelt Brewing Co</td>
            <td>Gaylord</td>
        </tr>
        <tr>
            <td>6748</td>
            <td>e2876f5d-0f50-4cae-8d00-76b8edfe7ba0</td>
            <td>St Ambrose Cellars</td>
            <td>micro</td>
            <td>841 S Pioneer Rd</td>
            <td>None</td>
            <td>None</td>
            <td>Beulah</td>
            <td>Michigan</td>
            <td>49617</td>
            <td>United States</td>
            <td>2313834262</td>
            <td><a href=http://www.stambrose-mead-wine.com>http://www.stambrose-mead-wine.com</a></td>
            <td>None</td>
            <td>None</td>
            <td>102</td>
            <td>St Ambrose Cellars</td>
            <td>Beulah</td>
        </tr>
        <tr>
            <td>6786</td>
            <td>481a0ead-5e95-451e-a431-ba8972b7e5ea</td>
            <td>Starving Artist Brewing</td>
            <td>micro</td>
            <td>634 S Stiles Rd</td>
            <td>None</td>
            <td>None</td>
            <td>Ludington</td>
            <td>Michigan</td>
            <td>49431-9311</td>
            <td>United States</td>
            <td>2317941399</td>
            <td><a href=http://starvingartist.beer>http://starvingartist.beer</a></td>
            <td>None</td>
            <td>None</td>
            <td>103</td>
            <td>Starving Artist Brewing Co</td>
            <td>Ludington</td>
        </tr>
        <tr>
            <td>6874</td>
            <td>906c9e56-8260-4ea5-ade4-4d73e3dd7246</td>
            <td>Stormcloud Brewing Company</td>
            <td>brewpub</td>
            <td>303 Main St</td>
            <td>None</td>
            <td>None</td>
            <td>Frankfort</td>
            <td>Michigan</td>
            <td>49635-9047</td>
            <td>United States</td>
            <td>2316519080</td>
            <td><a href=http://www.stormcloudbrewing.com>http://www.stormcloudbrewing.com</a></td>
            <td>-86.24100175</td>
            <td>44.63290605</td>
            <td>105</td>
            <td>Stormcloud Brewing Co</td>
            <td>Frankfort</td>
        </tr>
        <tr>
            <td>6874</td>
            <td>906c9e56-8260-4ea5-ade4-4d73e3dd7246</td>
            <td>Stormcloud Brewing Company</td>
            <td>brewpub</td>
            <td>303 Main St</td>
            <td>None</td>
            <td>None</td>
            <td>Frankfort</td>
            <td>Michigan</td>
            <td>49635-9047</td>
            <td>United States</td>
            <td>2316519080</td>
            <td><a href=http://www.stormcloudbrewing.com>http://www.stormcloudbrewing.com</a></td>
            <td>-86.24100175</td>
            <td>44.63290605</td>
            <td>106</td>
            <td>Stormcloud Brewery & Parkview Taproom</td>
            <td>Frankfort</td>
        </tr>
        <tr>
            <td>6875</td>
            <td>3ba0c759-64a6-4c73-8f41-d5d2317ce86d</td>
            <td>Stormcloud Brewing Company Production Facility</td>
            <td>micro</td>
            <td>366 Parkview Ln</td>
            <td>None</td>
            <td>None</td>
            <td>Frankfort</td>
            <td>Michigan</td>
            <td>49635</td>
            <td>United States</td>
            <td>None</td>
            <td>None</td>
            <td>-86.2218117</td>
            <td>44.6373933</td>
            <td>106</td>
            <td>Stormcloud Brewery & Parkview Taproom</td>
            <td>Frankfort</td>
        </tr>
        <tr>
            <td>6875</td>
            <td>3ba0c759-64a6-4c73-8f41-d5d2317ce86d</td>
            <td>Stormcloud Brewing Company Production Facility</td>
            <td>micro</td>
            <td>366 Parkview Ln</td>
            <td>None</td>
            <td>None</td>
            <td>Frankfort</td>
            <td>Michigan</td>
            <td>49635</td>
            <td>United States</td>
            <td>None</td>
            <td>None</td>
            <td>-86.2218117</td>
            <td>44.6373933</td>
            <td>105</td>
            <td>Stormcloud Brewing Co</td>
            <td>Frankfort</td>
        </tr>
        <tr>
            <td>7046</td>
            <td>3bc7477e-6b51-4ec4-9879-106902e23f21</td>
            <td>Tecumseh Brewing Company</td>
            <td>brewpub</td>
            <td>128 W Chicago Blvd</td>
            <td>None</td>
            <td>None</td>
            <td>Tecumseh</td>
            <td>Michigan</td>
            <td>49286-1553</td>
            <td>United States</td>
            <td>5178151726</td>
            <td><a href=http://www.tecumsehbrewingco.com>http://www.tecumsehbrewingco.com</a></td>
            <td>-83.946191</td>
            <td>42.004052</td>
            <td>107</td>
            <td>Tecumseh Brewing Co</td>
            <td>Tecumseh</td>
        </tr>
        <tr>
            <td>7067</td>
            <td>00a78d20-3da2-4f63-9270-44b4722f4883</td>
            <td>Tenacity Brewing</td>
            <td>micro</td>
            <td>119 N Grand Traverse St</td>
            <td>None</td>
            <td>None</td>
            <td>Flint</td>
            <td>Michigan</td>
            <td>48503-5620</td>
            <td>United States</td>
            <td>8103396676</td>
            <td><a href=http://www.tenacitybrewing.com>http://www.tenacitybrewing.com</a></td>
            <td>-83.69663179</td>
            <td>43.01522788</td>
            <td>108</td>
            <td>Tenacity Brewery</td>
            <td>Flint</td>
        </tr>
        <tr>
            <td>7150</td>
            <td>ad43dd25-02a6-4196-b268-afb686787e37</td>
            <td>The Distant Whistle Brewhouse</td>
            <td>micro</td>
            <td>118 S Main St</td>
            <td>None</td>
            <td>None</td>
            <td>Vicksburg</td>
            <td>Michigan</td>
            <td>49097-1289</td>
            <td>United States</td>
            <td>2693707549</td>
            <td><a href=http://www.distantwhistle.com>http://www.distantwhistle.com</a></td>
            <td>-85.53248776</td>
            <td>42.11909502</td>
            <td>110</td>
            <td>The Distant Whistle Brewhouse</td>
            <td>Vicksburg</td>
        </tr>
        <tr>
            <td>7161</td>
            <td>2d83cc3f-3967-41ae-a3b9-7cd75c40edc3</td>
            <td>The Filling Station Microbrewery</td>
            <td>micro</td>
            <td>642 Railroad Pl</td>
            <td>None</td>
            <td>None</td>
            <td>Traverse City</td>
            <td>Michigan</td>
            <td>49686-3633</td>
            <td>United States</td>
            <td>2319468168</td>
            <td><a href=http://www.thefillingstationmicrobrewery.com>http://www.thefillingstationmicrobrewery.com</a></td>
            <td>-85.609476</td>
            <td>44.7580963</td>
            <td>111</td>
            <td>The Filling Station Microbrewery</td>
            <td>Traverse City</td>
        </tr>
        <tr>
            <td>7182</td>
            <td>fedd7659-93ad-477b-b1fd-114ea7286a0b</td>
            <td>The Highway Brewing Co</td>
            <td>brewpub</td>
            <td>209 W Houghton Ave</td>
            <td>None</td>
            <td>None</td>
            <td>West Branch</td>
            <td>Michigan</td>
            <td>48661-1219</td>
            <td>United States</td>
            <td>9897090716</td>
            <td><a href=http://www.highwaybrewingco.com>http://www.highwaybrewingco.com</a></td>
            <td>-84.23751651</td>
            <td>44.27635208</td>
            <td>112</td>
            <td>The Highway Brewing Co</td>
            <td>West Branch</td>
        </tr>
        <tr>
            <td>7199</td>
            <td>81af98d0-0d70-4e84-8b4f-2804dd75e227</td>
            <td>The Livery</td>
            <td>brewpub</td>
            <td>190 5th St</td>
            <td>None</td>
            <td>None</td>
            <td>Benton Harbor</td>
            <td>Michigan</td>
            <td>49022-3484</td>
            <td>United States</td>
            <td>2699258760</td>
            <td><a href=http://www.liverybrew.com>http://www.liverybrew.com</a></td>
            <td>-86.45330019</td>
            <td>42.11813538</td>
            <td>113</td>
            <td>The Livery</td>
            <td>Benton Harbor</td>
        </tr>
        <tr>
            <td>7213</td>
            <td>7550c372-1e49-49f5-b7d9-764e260fdba5</td>
            <td>The Mitten Brewing Company   Saugatuck</td>
            <td>brewpub</td>
            <td>329 Water St</td>
            <td>None</td>
            <td>None</td>
            <td>Saugatuck</td>
            <td>Michigan</td>
            <td>49453</td>
            <td>United States</td>
            <td>6166085612</td>
            <td>None</td>
            <td>-86.20524129</td>
            <td>42.65689129</td>
            <td>115</td>
            <td>The Mitten Brewing Co</td>
            <td>Saugatuck</td>
        </tr>
        <tr>
            <td>7294</td>
            <td>33253e4f-6acd-4cf7-8ed6-2fbabee79b2b</td>
            <td>Third Nature Brewing Co</td>
            <td>planning</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
            <td>Rockford</td>
            <td>Michigan</td>
            <td>49341</td>
            <td>United States</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
            <td>117</td>
            <td>Third Nature Brewing Co</td>
            <td>Rockford</td>
        </tr>
        <tr>
            <td>7373</td>
            <td>37cde8b1-1aaf-4181-aee9-e6481769d59c</td>
            <td>Tilted Axis Brewing Company</td>
            <td>brewpub</td>
            <td>303 W Nepessing St</td>
            <td>None</td>
            <td>None</td>
            <td>Lapeer</td>
            <td>Michigan</td>
            <td>48446-2105</td>
            <td>United States</td>
            <td>8109694477</td>
            <td><a href=http://www.tiltedaxis.beer>http://www.tiltedaxis.beer</a></td>
            <td>-83.31247549</td>
            <td>43.05377576</td>
            <td>121</td>
            <td>Tilted Axis Brewing Co</td>
            <td>Lapeer</td>
        </tr>
        <tr>
            <td>7438</td>
            <td>8e300f8a-fce3-446f-bbb4-aac35ff1a08e</td>
            <td>Townies Brewery</td>
            <td>brewpub</td>
            <td>2350 W Liberty Rd</td>
            <td>None</td>
            <td>None</td>
            <td>Ann Arbor</td>
            <td>Michigan</td>
            <td>48103</td>
            <td>United States</td>
            <td>None</td>
            <td>None</td>
            <td>-83.7891745</td>
            <td>42.2730356</td>
            <td>122</td>
            <td>Townies Brewery</td>
            <td>Ann Arbor</td>
        </tr>
        <tr>
            <td>7453</td>
            <td>9a256c21-3a8b-480b-9cb5-fc52084fedca</td>
            <td>Traffic Jam and Snug</td>
            <td>brewpub</td>
            <td>4268 2nd Ave</td>
            <td>None</td>
            <td>None</td>
            <td>Detroit</td>
            <td>Michigan</td>
            <td>48201-1706</td>
            <td>United States</td>
            <td>3138319470</td>
            <td><a href=http://www.trafficjamdetroit.com>http://www.trafficjamdetroit.com</a></td>
            <td>-83.086546</td>
            <td>42.384371</td>
            <td>123</td>
            <td>Traffic Jam & Snug</td>
            <td>Detroit</td>
        </tr>
        <tr>
            <td>7456</td>
            <td>4ed098f5-5ff6-40b9-b238-538961222e90</td>
            <td>Trail Point Brewing Co</td>
            <td>micro</td>
            <td>6035 Lake Michigan Dr</td>
            <td>None</td>
            <td>None</td>
            <td>Allendale</td>
            <td>Michigan</td>
            <td>49401-9101</td>
            <td>United States</td>
            <td>6168952739</td>
            <td><a href=http://www.trailpointbrewing.com>http://www.trailpointbrewing.com</a></td>
            <td>-85.93541536</td>
            <td>42.97207782</td>
            <td>124</td>
            <td>Trail Point Brewing Co</td>
            <td>Allendale</td>
        </tr>
        <tr>
            <td>7458</td>
            <td>c9f36d18-efb4-419c-8acf-b4598bbf2326</td>
            <td>Transient Artisan Ales</td>
            <td>micro</td>
            <td>4229 Lake St</td>
            <td>None</td>
            <td>None</td>
            <td>Bridgman</td>
            <td>Michigan</td>
            <td>49106-9109</td>
            <td>United States</td>
            <td>None</td>
            <td>None</td>
            <td>-86.55570937</td>
            <td>41.94299804</td>
            <td>125</td>
            <td>Transient Artisan Ales</td>
            <td>Bridgman</td>
        </tr>
        <tr>
            <td>7473</td>
            <td>100fc1b1-c30f-4aeb-80bd-ab2914e7483a</td>
            <td>Tri City Brewing Co</td>
            <td>micro</td>
            <td>4170 Shrestha Dr</td>
            <td>None</td>
            <td>None</td>
            <td>Bay City</td>
            <td>Michigan</td>
            <td>48706-2184</td>
            <td>United States</td>
            <td>9896861340</td>
            <td><a href=http://www.tricitybrewing.com>http://www.tricitybrewing.com</a></td>
            <td>None</td>
            <td>None</td>
            <td>126</td>
            <td>Tri City Brewing Co</td>
            <td>Bay City</td>
        </tr>
        <tr>
            <td>7657</td>
            <td>fe976b5b-744d-4ba8-b9b8-af2c2e249b8b</td>
            <td>Upper Hand Brewery</td>
            <td>micro</td>
            <td>3525 Airport Rd</td>
            <td>None</td>
            <td>None</td>
            <td>Escanaba</td>
            <td>Michigan</td>
            <td>49829-1096</td>
            <td>United States</td>
            <td>2693822338</td>
            <td><a href=http://www.upperhandbrewery.com>http://www.upperhandbrewery.com</a></td>
            <td>-87.097528</td>
            <td>45.7164279</td>
            <td>128</td>
            <td>Upper Hand Brewery</td>
            <td>Escanaba</td>
        </tr>
        <tr>
            <td>7683</td>
            <td>eae54355-ebca-400e-a9d9-7ce4ce866341</td>
            <td>Urbanrest Brewing Company</td>
            <td>micro</td>
            <td>2615 Wolcott St</td>
            <td>None</td>
            <td>None</td>
            <td>Ferndale</td>
            <td>Michigan</td>
            <td>48220-1422</td>
            <td>United States</td>
            <td>None</td>
            <td><a href=http://www.urbanrest.com>http://www.urbanrest.com</a></td>
            <td>-83.1274188</td>
            <td>42.4679929</td>
            <td>130</td>
            <td>Urbanrest Brewing Co</td>
            <td>Ferndale</td>
        </tr>
        <tr>
            <td>7810</td>
            <td>86ac6974-4c49-4e84-8d51-47f97fda5ac4</td>
            <td>WAR WATER BREWERY</td>
            <td>brewpub</td>
            <td>201 N Riverside Ave Ste C1-11</td>
            <td>None</td>
            <td>None</td>
            <td>Saint Clair</td>
            <td>Michigan</td>
            <td>48079-5498</td>
            <td>United States</td>
            <td>8102893921</td>
            <td><a href=http://www.warwaterbrewery.com>http://www.warwaterbrewery.com</a></td>
            <td>None</td>
            <td>None</td>
            <td>131</td>
            <td>War Water Brewery/Distillery</td>
            <td>Saint Clair</td>
        </tr>
        <tr>
            <td>7835</td>
            <td>309cfb01-0cde-4775-91c6-95e492b5eb4b</td>
            <td>Watermark Brewing Company</td>
            <td>micro</td>
            <td>5781 Saint Joseph Ave</td>
            <td>None</td>
            <td>None</td>
            <td>Stevensville</td>
            <td>Michigan</td>
            <td>49127-1238</td>
            <td>United States</td>
            <td>2692810872</td>
            <td><a href=http://www.watermarkbrewing.com>http://www.watermarkbrewing.com</a></td>
            <td>-86.51963025</td>
            <td>42.01286668</td>
            <td>132</td>
            <td>Watermark Brewing Co</td>
            <td>Stevensville</td>
        </tr>
        <tr>
            <td>7841</td>
            <td>8e46e558-55b6-4c93-bf99-2ba3f2ba2a59</td>
            <td>Waypost Brewing Co</td>
            <td>planning</td>
            <td>None</td>
            <td>None</td>
            <td>None</td>
            <td>Fennville</td>
            <td>Michigan</td>
            <td>49408</td>
            <td>United States</td>
            <td>6169162362</td>
            <td><a href=http://waypostbeer.com>http://waypostbeer.com</a></td>
            <td>None</td>
            <td>None</td>
            <td>133</td>
            <td>Waypost Brewing Co</td>
            <td>Fennville</td>
        </tr>
        <tr>
            <td>8015</td>
            <td>fd24f694-6436-458c-a0d5-d35ab5ff4497</td>
            <td>Witch's Hat Brewing</td>
            <td>micro</td>
            <td>601 S Lafayette St</td>
            <td>None</td>
            <td>None</td>
            <td>South Lyon</td>
            <td>Michigan</td>
            <td>48178-1453</td>
            <td>United States</td>
            <td>2484862595</td>
            <td><a href=http://www.witchshatbrewing.com>http://www.witchshatbrewing.com</a></td>
            <td>-83.65099308</td>
            <td>42.45542522</td>
            <td>134</td>
            <td>Witch's Hat Brewing Co</td>
            <td>South Lyon</td>
        </tr>
        <tr>
            <td>8027</td>
            <td>932f3a05-590e-49dc-bba5-0c6c7c174125</td>
            <td>Wolverine State Brewing Co</td>
            <td>brewpub</td>
            <td>2019 W Stadium Blvd # 103</td>
            <td>None</td>
            <td>None</td>
            <td>Ann Arbor</td>
            <td>Michigan</td>
            <td>48103-4557</td>
            <td>United States</td>
            <td>7343692990</td>
            <td><a href=http://www.wolverinebeer.com>http://www.wolverinebeer.com</a></td>
            <td>-83.7745473</td>
            <td>42.2694316</td>
            <td>135</td>
            <td>Wolverine State Brewing</td>
            <td>Ann Arbor</td>
        </tr>
    </tbody>
</table>



Nice! We found 98 matches between the two tables! With a smaller data set such as this, we could alternatively download the dataset into excel and manually check for matching breweries as well.

Now that we have a dataset of dog-friendly breweries, let's create a data viz! We'll first upload the data into a new SQL table, then we'll connect the data to Tableau and build our data viz.


```sql
%%sql

CREATE TABLE dog_brewery_join AS
SELECT id, name, brewery_type, address_1, mich_brew.city, state_province, postal_code, country, phone, longitude, latitude
FROM
  (SELECT *
   FROM breweries
   WHERE breweries.state_province = 'Michigan'
   AND breweries.name <> 'TBD'
  )AS mich_brew
JOIN dog_breweries ON SIMILARITY (mich_brew.name, dog_breweries.brewery_name) > 0.2
and mich_brew.city = dog_breweries.city
and UPPER(SUBSTRING(mich_brew.name, 1, POSITION(' ' IN mich_brew.name))) = 
    UPPER(SUBSTRING(dog_breweries.brewery_name, 1, POSITION(' ' IN dog_breweries.brewery_name)))
ORDER BY mich_brew.name
```


<span style="None">Running query in &#x27;brewery_database&#x27;</span>



<span style="color: green">98 rows affected.</span>





    




```python
%%HTML
<div class='tableauPlaceholder' id='viz1689990789504' style='position: relative'><noscript><a href='#'><img alt='Dog-Friendly Breweries in Michigan ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Do&#47;DogFriendlyMichiganBreweries&#47;Dog-FriendlyBreweriesinMichigan&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='DogFriendlyMichiganBreweries&#47;Dog-FriendlyBreweriesinMichigan' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Do&#47;DogFriendlyMichiganBreweries&#47;Dog-FriendlyBreweriesinMichigan&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /><param name='filter' value='publish=yes' /></object></div>                <script type='text/javascript'>                    var divElement = document.getElementById('viz1689990789504');                    var vizElement = divElement.getElementsByTagName('object')[0];                    if ( divElement.offsetWidth > 800 ) { vizElement.style.width='100%';vizElement.style.height=(divElement.offsetWidth*0.75)+'px';} else if ( divElement.offsetWidth > 500 ) { vizElement.style.width='100%';vizElement.style.height=(divElement.offsetWidth*0.75)+'px';} else { vizElement.style.width='100%';vizElement.style.height='727px';}                     var scriptElement = document.createElement('script');                    scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';                    vizElement.parentNode.insertBefore(scriptElement, vizElement);                </script>
```


<div class='tableauPlaceholder' id='viz1689990789504' style='position: relative'><noscript><a href='#'><img alt='Dog-Friendly Breweries in Michigan ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Do&#47;DogFriendlyMichiganBreweries&#47;Dog-FriendlyBreweriesinMichigan&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='DogFriendlyMichiganBreweries&#47;Dog-FriendlyBreweriesinMichigan' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Do&#47;DogFriendlyMichiganBreweries&#47;Dog-FriendlyBreweriesinMichigan&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /><param name='filter' value='publish=yes' /></object></div>                <script type='text/javascript'>                    var divElement = document.getElementById('viz1689990789504');                    var vizElement = divElement.getElementsByTagName('object')[0];                    if ( divElement.offsetWidth > 800 ) { vizElement.style.width='100%';vizElement.style.height=(divElement.offsetWidth*0.75)+'px';} else if ( divElement.offsetWidth > 500 ) { vizElement.style.width='100%';vizElement.style.height=(divElement.offsetWidth*0.75)+'px';} else { vizElement.style.width='100%';vizElement.style.height='727px';}                     var scriptElement = document.createElement('script');                    scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';                    vizElement.parentNode.insertBefore(scriptElement, vizElement);                </script>



# References

1. Bajaj, T. (2023, February 13). *Program for distance between two points on earth*. GeeksforGeeks. https://www.geeksforgeeks.org/program-distance-two-points-earth/

2. McKinney, K. (2022, July). Dog Friendly Breweries. *Review Before Your Next Brew*. https://michiganbreweryreview.com/michigan-dog-friendly-breweries/2018/8/2/the-ever-growing-list-of-k-9-friendly-breweries?format=amp

3. Chris Mears. Openbrewerydb. (2023). GitHub repository. https://github.com/openbrewerydb/openbrewerydb


```python

```
