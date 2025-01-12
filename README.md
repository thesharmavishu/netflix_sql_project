# Netflix Movies and TV Shows Data Analysis using SQL
![Netflix Logo](https://pngimg.com/uploads/netflix/netflix_PNG25.png)
## Objective
## Schema 
-- Netflix Project  --

create table netflix (
 show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);

select * from netflix;

select 
count(*) as Total_content
from netflix;

select distinct type from netflix;

-- 15 Business Problems --

 -- 1. Count the number of movies vs TV shows ?
 
select * from netflix 

select type, 
count(*) as total_content
from netflix
group by type

-- 2. Find the most common rating for movies and Tv Shows ?

select type , rating from netflix

select 
type, 
rating ,
count(*)
from netflix
Group by 1,2
order by 1, 3 desc
--------------------------------
select 
type, 
rating ,
count(*),
Rank() over(partition by type Order By count(*) Desc) as ranking
from netflix
Group by 1,2
--order by 1, 3 desc
---------------------------------
select 
type, 
rating ,
count(*),
Rank() over(partition by type Order By count(*) Desc) as ranking
from netflix
Group by 1,2
----------------------------------
select 
type, 
rating 
from
(
select 
type, 
rating ,
count(*),
Rank() over(partition by type Order By count(*) Desc) as ranking
from netflix
Group by 1,2
) as T1
where 
ranking = 1

-- 3. List all movies released in specific year (e.g, 2020) ?

select * from netflix
where type ='Movie' and release_year = 2020


-- 4. Find the top 5 countries with the most content on netflix ?

select country,
count(show_id) as Total_content
from netflix
group by 1
----------------
select
  Unnest (String_to_array(country, ',')) as new_country
  from netflix
---------------------
select 
   Unnest (String_to_array(country, ',')) as new_country,
   count(show_id) as Total_content
   from netflix
group by 1
order by 2 desc
limit 5

-- 5. Identify the longest movie ?

select * from Netflix
    where 
    type = 'Movie' AND 
	duration = (Select Max(duration) from Netflix)
	
-- 6.Find content added in last 5 years ?
 
select * from Netflix

select *,
      To_Date(date_added, 'Month DD, YYYY')
		  from Netflix
------------------------
 select * From netflix
 where
      To_Date(date_added, 'Month DD, YYYY') >= Current_date - Interval '5 Years'
	  

-- 7. Find all the movies/TV shows by director 'Rajiv Chilaka' ??

select * from netflix
where director = 'Rajiv Chilaka'
--------------------------------

select * from netflix
where director like '%Rajiv Chilaka%'
------------------------------
select * from netflix
where director ilike '%Rajiv Chilaka%'


-- 8. List all TV shows with more than 5 seasons ??

select * from netflix
where type = 'TV Show'
----------------------
select *, 
split_part(duration,' ', 1) as Seasons from netflix
where type = 'TV Show'
----------------------
select * from netflix
where type = 'TV Show'
and
split_part(duration,' ', 1):: numeric > 5

-- 9. Count the number of content items in  each gener ?

select  listed_in , show_id, 
STRING_TO_ARRAY(listed_in, ',')
from netflix
-------------------------------
select  listed_in , show_id, 
unnest (STRING_TO_ARRAY(listed_in, ','))
from netflix
-------------------------------
select   
unnest (STRING_TO_ARRAY(listed_in, ',')) as genre,
count (show_id) as total_content
from netflix
group by 1

-- 10. Find each year the average number of content release by india on netflix. 
.return top 5 year with hioghest average content release ?

select 
   To_date(date_added, 'Month DD, YYYY') as Date, 
  *  From Netflix
where country = 'India'
------------------------
select 
  Extract(Year from To_date(date_added, 'Month DD, YYYY')) as year,
  count(*)
   From Netflix
where country = 'India'
Group by 1
--------------------------
select 
  Extract(Year from To_date(date_added, 'Month DD, YYYY')) as year,
  count(*) as yearly_content,
 Round(
	 count(*)::numeric/(Select count(*) from netflix where country = 'India') * 100 
	 ,2) as avg_content_per_year
   From Netflix
where country = 'India'
Group by 1

--11 List all the movies that are documentry ?

select * from netflix
where listed_in ilike '%Documentaries%'

-- 12. Find all content without a Director ?

Select* from Netflix
Where director is null

-- 13. Fimd how many movies Actor 'Salman Khan' appeared in last 10 Years ?
 
 Select * from netflix
 where  casts ilike '%Salman Khan%'
 And 
 release_year > Extract(Year from Current_date) - 10
 
 -- 14. Find the top 10 actors who have appeared in the highest number of movies produced in India ?
 
 Select show_id , casts ,
 Unnest (String_to_array(casts, ','))  as Actor
 From Netflix
 ------------------------------
 Select 
 Unnest (String_to_array(casts, ','))  as Actor,
 Count(*) as total_count
 From Netflix
 where country ilike '%india%'
 Group by 1
 order by 2 desc
 limit 10
 
 -- 15. Categorize the content based on the presence of the keywords 'kill' and 'violence' in the
         description field. Label content containing these keywords as 'Bad' and all others content as 
		 'Good'. Count how many items fall into each category ?
		 
 Select * From Netflix
 where description ilike '%kill%'
 or
 description ilike '%violence%'
 --------------------------------
 Select * ,
  case 
  when description ilike '%kill%' OR
       description ilike '%violence%' then 'Bad_Content'
	   Else 'Good_Content'
	   End category
 From Netflix
 --------------------------------
 With new_table
 as
 (
 Select * ,
  case 
  when description ilike '%kill%' OR
       description ilike '%violence%' then 'Bad_Content'
	   Else 'Good_Content'
	   End category
 From Netflix
)
Select category ,
    count(*) as total_content
from new_table
Group by 1

