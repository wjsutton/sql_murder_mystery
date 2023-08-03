# SQL Murder Mystery
Solutions to the SQL Murder Mystery challenge by Knight Lab https://mystery.knightlab.com/

## :movie_camera: YouTube Walkthrough

<a href='https://youtu.be/w8DSLB8Wa2o'>
  <img src='https://github.com/wjsutton/sql_murder_mystery/blob/main/youtube_thumb.png?raw=true' width='80%' >
</a>

## :scroll: Solution Script

### 1. Find Information about Witnesses in crime_scene_report table
```
-- ​murder​ that occurred sometime on ​Jan.15, 2018​ 
-- and that it took place in ​SQL City​
SELECT *
FROM crime_scene_report
WHERE type = 'murder'
AND city = 'SQL City'
AND date = 20180115;
```
  
### 2. Find details about the witnesses in person table 
```
-- 2 witnesses. 
-- 1st witness lives at the last house on "Northwestern Dr". 
-- 2nd witness, named Annabel, lives somewhere on "Franklin Ave".

-- 1st Witness 14887	Morty Schapiro
SELECT *
FROM person
WHERE address_street_name = 'Northwestern Dr'
ORDER BY address_number DESC
LIMIT 1;

-- 2nd Witness 16371	Annabel Miller
SELECT *
FROM person
WHERE address_street_name = 'Franklin Ave'
AND LOWER(name) LIKE '%annabel%'
```


### 3. Lookup Witnesses in interview table
```
SELECT *
FROM interview
WHERE person_id IN (14887,16371);
```

### 4. Using details from interview table find the killer
```
-- "Get Fit Now Gym" bag started with "48Z" gold member
-- car with a plate that included "H42W"
-- my gym last week on January the 9th
SELECT p.*, ci.*
FROM drivers_license as dl
INNER JOIN person as p on dl.id = p.license_id
INNER JOIN get_fit_now_member as gf on p.id = gf.person_id
INNER JOIN get_fit_now_check_in as ci on gf.id = ci.membership_id
WHERE plate_number LIKE '%H42W%'
AND membership_status = 'gold'
AND check_in_date = 20180109;
```

### 5. Lookup killer's interview
```
SELECT *
FROM interview
WHERE person_id = 67318;
```
  
### 6. Find the mastermind
```
-- hired by a woman with a lot of money
-- around 5'5" (65") or 5'7" (67"), red hair, drives a Tesla Model S
-- attended the SQL Symphony Concert 3 times in December 2017

-- Common Table Expression (CTE) to find people that attended the SQL Concert 3+ times in December 2017
-- Then joined to person table filtered by the description given
WITH CTE AS (
SELECT 
person_id,
COUNT(*) as visits
FROM facebook_event_checkin
WHERE date BETWEEN 20171201 AND 20171231
AND event_name = 'SQL Symphony Concert'
GROUP BY person_id
HAVING COUNT(*) >=3
)

SELECT p.*, fb.*
FROM drivers_license as dl
INNER JOIN person as p on dl.id = p.license_id
INNER JOIN CTE as fb on fb.person_id = p.id
WHERE hair_color = 'red'
AND height >= 65
AND height <= 67
AND car_make = 'Tesla'
AND car_model ='Model S' 
and gender = 'female';
```
