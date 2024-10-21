# Dario Giordano for ShopFully - Data Engineer assessment test

Hi ShopFully Data Team! First of all, I wish to thank you for giving me te opportunity to take part in this part of the interview.\
This assessment test was taken on a Windows machine. I used Visual Studio Code as my IDE of choice and pgAdmin to manage and query the PostgreSQL database.\

## Part 1 - Data modeling
For this part of the test, I read the OpenWeather API docs and selected some of the output values based on the given requirements. Then, I created a PostgreSQL database on my localhost server called 'weatherdb', and proceeded to create the necessary tables.

``` sql
CREATE TABLE city (
    city_id SERIAL PRIMARY KEY,
    city_name VARCHAR(50) NOT NULL
);
CREATE TABLE weather (
    weather_id SERIAL PRIMARY KEY,
    city_id INT REFERENCES city(city_id),
    timestamp TIMESTAMPTZ NOT NULL,
    temperature DECIMAL(5,2),
    min_temperature DECIMAL(5,2),
    max_temperature DECIMAL(5,2),
    humidity INT,
    weather_condition VARCHAR(50),
    wind_speed DECIMAL(5,2)
);
```

Using the built in tool in pgAdmin, I was also able to generate the entity relationship diagram.
![erdiag](/images/er_diagram.png)\
\
With the tables created, it was possible to write SQL queries to answer the requirements:\
**Distinct weather conditions in a given period**
``` sql
SELECT COUNT(DISTINCT w.weather_condition) as num_unique_conditions
FROM weather w
WHERE w.timestamp BETWEEN '2024-09-01' AND '2024-11-01' -- Replace with desired period
```
**Most common conditions in a given period by city**
``` sql
SELECT c.city_name, w.weather_condition, COUNT(*) AS condition_count
FROM weather w
JOIN city c ON w.city_id = c.city_id
WHERE w.timestamp BETWEEN '2024-09-01' AND '2024-10-01' -- Replace with desired period
GROUP BY c.city_name, w.weather_condition
ORDER BY c.city_name, condition_count DESC;
``` 
**Temperature averages in a given period per city**
``` sql
SELECT c.city_name, AVG(w.temperature) as avg_temp
FROM weather w
join city c ON w.city_id = c.city_id
WHERE w.timestamp BETWEEN '2024-09-01' AND '2024-10-01' -- Replace with desired period
GROUP BY c.city_name;
``` 
**City with the highest temperature in a given period**
``` sql
SELECT c.city_name, MAX(w.max_temperature) as max_temp
FROM weather w
JOIN city c ON w.city_id = c.city_id
WHERE w.timestamp BETWEEN '2024-09-01' AND '2024-10-01' -- Replace with desired period
GROUP BY c.city_name
ORDER BY max_temp DESC
LIMIT 1;
``` 
**City with the highest daily temperature variation in a given period**
``` sql
SELECT 
    c.city_name,
    w.timestamp,
    w.min_temperature,
    w.max_temperature,
    (w.max_temperature - w.min_temperature) AS temp_diff
FROM weather w
JOIN city c
ON w.city_id = c.city_id
ORDER BY temp_diff DESC
LIMIT 1;
``` 
**City with the strongest wind in a given period**
``` sql
SELECT c.city_name, MAX(w.wind_speed) as max_wind_speed
FROM weather w
JOIN city c ON w.city_id = c.city_id
WHERE w.timestamp BETWEEN '2024-09-01' AND '2024-10-01' -- Replace with desired period
GROUP BY c.city_name
ORDER BY max_wind_speed desc
LIMIT 1;
``` 

## Part 2 - Script writing
The second part of the assignment is found in the script.ipynb file, where I added markdown and comments for clarity.