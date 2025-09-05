# Airbnb_case_study
In this project, I explored user behavior and country information using SQL queries on the Airbnb sessions_data and countries datasets. The analysis was structured into beginner, intermediate, and advanced levels, demonstrating progressive data analysis and problem-solving skills.
---
## 📁 File
Contains 14 SQL queries with outputs, each designed to solve a specific analytics problem.

---
## Beginner Level

### 1.Show all distinct device types used in sessions_data.
```sql
select distinct device_type from sessions_data;
```
**Insight** : The mostly used devices in the sessions_data are Windows Desktop, iPhone, Mac Desktop etc.

---
### 2.Count how many actions each user (user_id) has performed.
```sql
select user_id, count(action) from sessions_data
group by user_id
order by count(action) desc;
```

**Insight** : The top user has performed 1039 actions while the last user has performed 1 action according to the data set.

---
### 3.Find the top 5 countries with the largest land area (destination_km2).
```sql
select country_destination, destination_km2 from countries
order by destination_km2 desc
limit 5;
```

**Insight** : The top 5 countries with the largest land area are CA, US, AU, FR, ES.

---
### 4.List all user_ids where secs_elapsed is NULL.
```sql
select distinct user_id from sessions_data
where secs_elapsed is null; 
```

**Insight** : Detected missing or incomplete data with users where session times are NULL.

---
## Intermediate Level

### 5.Get the average secs_elapsed per device_type.
```sql
select device_type, avg(secs_elapsed) from sessions_data
group by device_type;
```

**Insight** : Here we can see which type of device has the most amount of time spent on the app showing usage intensity.
              Windows Desktop, iPhone are the top two device types.

---
### 6.Find the most common action_detail across all users.
```sql
select action_detail, count(action_detail) as most_common_action from sessions_data
group by action_detail
order by most_common_action desc; 
```

**Insight** : Found the most frequent interaction type (action_detail).

---
### 7.Retrieve users who performed more than 100 actions.
```sql
select user_id, count(action) from sessions_data
group by user_id
having count(action) > 100
order by count(action) desc;
```

**Insight** : Isolated highly active users (those with >100 actions). The most no. of actions performed by a single user id is 1039

---

### 8.Join sessions_data and countries to show which users interacted with listings from the US.
```sql
select S.user_id, U.country_destination from sessions_data S
join users U on U.id = S.user_id
join countries C on C.country_destination = U.country_destination
where U.country_destination = "US"
group by S.user_id, U.country_destination; 
```

**Insight** : Performed table joins to connect user behavior with geographic destinations (e.g., US interactions). 
              Here is a list of users who have checked the listings from the US.


---

## Advanced Level

### 9.Rank countries by distance_km and show the top 3 farthest from the US.
```sql
select country_destination, distance_km from countries
order by distance_km desc
limit 3;
```

**Insight** : The top three countries farthest from the US are Australia, Italy, Denmark.

---

### 10.For each user_id, calculate the total time spent on Airbnb.
```sql
select user_id, round(sum(secs_elapsed)/60,2) as minutes_spent from sessions_data
group by user_id;
```

**Insight** : Summed total time spent on the platform by each user.

---

### 11.Find the proportion of click actions vs view actions for each device_type.
```sql
select device_type, sum(case when action_type = "view" then 1 else 0 end) as total_view, sum( case when action_type = "click" then 1 else 0 end) as total_clicks,
round(sum(case when action_type = "click" then 1 else 0 end)/nullif(sum(case when action_type = "view" then 1 else 0 end),0),2) as click_to_view_ratio from sessions_data
group by device_type; 
```

**Insight** : Measured proportions of action types (click vs. view by device).

---

### 12.Identify users who have used both desktop and mobile devices.
```sql
select user_id from sessions_data
group by user_id
having sum(case when device_type in ("iPhone","Android Phone") then 1 else 0 end) > 0 and
sum(case when device_type in ("Windows Desktop","Mac Desktop","Linux Desktop") then 1 else 0 end) > 0;
```

**Insight** : Identified multi-device users (desktop + mobile) .i.e. users who have accessed the app from multiple devices.

---

### 13.Compare the average session time of users who clicked confirm_email_link vs. those who didn’t.
```sql
with cte as (select user_id, max(case when action_detail = "confirm_email_link" then 1 else 0 end) as clicked_email from sessions_data
group by user_id)

select c.clicked_email, avg(s.secs_elapsed) as avg_session_time from sessions_data s 
join cte c on c.user_id = s.user_id
where s.secs_elapsed is not null
group by c.clicked_email;
```
1 - users who clicked confirm_email_link 
0 - users who did not click on confirm_email_link.


**Insight** : Compared engagement based on specific actions (email confirmation). 
              Users who have clicked on the link has spent an average of 23096.74 secs whereas 
              the users who did not click on the link have spent an average of 16658.09 secs.


---

### 14.Find the country_destination whose language is most similar to English, using the language_levenshtein_distance.
```sql
select country_destination,destination_language, language_levenshtein_distance from countries
order by language_levenshtein_distance
limit 1;
```

**Insight** : Evaluated language similarity across destinations using Levenshtein distance and found out that Australia 
              has a linguistic closeness to English. 

---
