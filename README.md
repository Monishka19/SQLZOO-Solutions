# SQLZOO-Solutions
These are my solutions for [SQLZOO Tutorial](https://sqlzoo.net/wiki/SELECT_basics).

HAPPY LEARNING!

## Sections:
1. [SELECT basics](#select-basics)
2. [SELECT from WORLD](#select-from-world)
3. [SELECT from NOBEL](#select-from-nobel)
4. [SELECT in SELECT](#select-in-select)
5. [SUM and COUNT](#sum-and-count)
6. [JOIN](#join)
7. [More JOIN](#more-join)
8. [Using NULL](#using-null)
9. [Self JOIN](#self-join)

   

## SELECT basics
Introducing the world table of countries.

1.Modify it to show the population of Germany
```sql
  SELECT population 
  FROM world
  WHERE name = 'Germany';
```
2.Scandinavia
```sql
 SELECT name, population 
 FROM world
 WHERE name IN ('Sweden', 'Norway' ,'Denmark');
```
3.Just the right size
```sql
  SELECT name, area 
  FROM world
  WHERE area BETWEEN 200000 AND 250000;
```

## SELECT from WORLD
1. Introduction
```sql
SELECT name, continent, population 
FROM world;
```
2.Large Countries
```sql
SELECT name 
FROM world
WHERE population>200000000;
```
3.Per capita GDP
```sql
SELECT name,gdp/population as "per capita GDP" 
FROM world
WHERE population >= 200000000;
```
4.South America In millions
```sql
SELECT name,population/1000000 as "Population" 
FROM world
WHERE  continent = 'South America';
```
5.France, Germany, Italy
```sql
SELECT name,population 
FROM world
WHERE  name in ('France','Germany','Italy');
```
6.United
```sql
SELECT name 
FROM world
WHERE  name like 'United%';
```
7.Two ways to be big
```sql
SELECT name,population,area
FROM world
WHERE  area > 3000000
OR population > 250000000;
```
8.One or the other (but not both)
```sql
SELECT name,population,area
FROM world
WHERE  area > 3000000
XOR population > 250000000;
```
9.Rounding
```sql
SELECT name,round(population/1000000,2) as "Population",
            round(gdp/1000000000,2) as "GDP" 
FROM world
WHERE  continent = 'South America';
```
10.Trillion dollar economies
```sql
SELECT name,round(gdp/population,-3) as "Per capita gdp" 
FROM world
WHERE  gdp >=1000000000000;
```
11.Name and capital have the same length
```sql
SELECT name, capital
FROM world
WHERE length(name) = LENGTH(capital);
```
12.Matching name and capital
```sql
SELECT name, capital
FROM world  
WHERE   LEFT(name,1) = left(capital,1)
AND name <> capital;
```
13.All the vowels

```sql
SELECT name  
FROM world
WHERE name LIKE '%a%'
  AND name  LIKE '%e%'
  AND name  LIKE '%i%'
  AND name  LIKE '%o%'
  AND name  LIKE '%u%'
  AND name NOT LIKE '% %';
```


## SELECT from NOBEL
1.Winners from 1950
```sql
SELECT yr, subject, winner
FROM nobel
WHERE yr = 1950;
```
2.1962 Literature
```sql
SELECT winner
 FROM nobel
 WHERE yr = 1962
 AND subject = 'Literature';
```
3.Albert Einstein
```sql
SELECT  yr,subject
FROM nobel 
WHERE  winner =  'Albert Einstein';
```
4.Recent Peace Prizes
```sql
SELECT winner
FROM nobel
WHERE  subject = 'Peace' 
AND yr >= 2000;
```
5.Literature in the 1980's (Between is genrally inclusive,but not always!!!)
```sql
select yr,subject,winner
FROM nobel 
WHERE  subject = 'Literature'
AND yr between 1980 and 1989;
```
6.Only Presidents
```sql
SELECT * 
FROM nobel
WHERE winner IN ('Theodore Roosevelt','Woodrow Wilson',
	         'Jimmy Carter','Barack Obama');
```
7.John
```sql
SELECT winner 
FROM nobel
WHERE winner like 'John%';
```
8.Chemistry and Physics from different years
```sql
SELECT * 
FROM nobel
WHERE (Subject = 'Physics' and yr = 1980)
OR (Subject = 'Chemistry' and yr = 1984);
```
9.Exclude Chemists and Medics
```sql
SELECT * from nobel 
FROM nobel 
WHERE  (subject = 'Medicine' and yr < 1910)
OR (subject = 'Literature' and yr >= 2004);
```
10.Early Medicine, Late Literature
```sql
SELECT * 
FROM nobel
WHERE yr < 1910 AND subject = 'Medicine'
OR yr >= 2004 AND subject = 'Literature';
```
### Harder Questions

11.Umlaut
```sql
SELECT * 
FROM nobel
WHERE  winner like 'PETER GR%';
```
12.Apostrophe
```sql
SELECT * 
FROM nobel 
WHERE winner LIKE 'EUGENE O%';

```
12.Apostrophe (Alternative!)
```sql
SELECT * 
FROM nobel
WHERE winner = 'Eugene O''Neill';
```
13.Knights of the realm
```sql
SELECT winner,yr,subject 
FROM nobel
WHERE  winner like 'Sir%'
order by yr desc,winner;
```
14.Chemistry and Physics last (Reffer to video {quite a catch!!} )
```sql
 SELECT winner, subject 
 FROM nobel
 WHERE yr=1984
 ORDER BY subject in ('Physics','Chemistry'),subject,winner;
```
## SELECT in SELECT
1.Bigger than Russia
```sql
SELECT name 
FROM world
WHERE population > (SELECT population 
                    FROM world
                    WHERE name='Russia');
```
2.Richer than UK
```sql
SELECT name 
FROM world
WHERE continent = 'Europe'
AND gdp/population > (SELECT gdp/population
                           FROM world
                           WHERE name = 'United Kingdom');
```
3.Neighbours of Argentina and Australia
```sql
SELECT name, continent FROM world
 WHERE continent IN (SELECT continent FROM world
                     WHERE name IN ('Argentina','Australia'));
 ORDER BY name
```
4.Between Canada and Poland
```sql
SELECT name,population 
FROM world
WHERE population > (SELECT population 
                    FROM world 
		    WHERE name  = 'Canada')
AND   population < (SELECT population 
                    FROM world 
		    WHERE name  = 'Poland');
```
       
5.Percentages of Germany
```sql
SELECT name, 
CONCAT(ROUND(population/(SELECT population 
                         FROM world
                         WHERE name = 'Germany')*100,0),'%')
FROM world 
WHERE continent = 'Europe';
```
6.Bigger than every country in Europe
```sql
SELECT name 
FROM world
  WHERE gdp > (SELECT max(gdp) FROM world
                   WHERE gdp > 0 AND continent = 'Europe');
```
6.Bigger than every country in Europe(Suggested Alternative!)
```sql
SELECT name 
FROM world
  WHERE gdp > ALL(SELECT gdp FROM world
                   WHERE gdp > 0 AND continent = 'Europe');
```
7.Largest in each continent
```sql
--This is an example of a self join!
SELECT continent, name, area 
FROM world x
WHERE area >= ALL(SELECT area 
                  FROM world y
                  WHERE y.continent=x.continent
                  AND area>0);
```
7.Largest in each continent (Alternative!)
```sql
SELECT continent, name, area 
FROM world 
WHERE area in (SELECT max(area) 
               FROM world
               group by continent);
```
8.First country of each continent (alphabetically)
```sql
SELECT continent, name FROM world x
WHERE name <= ALL(SELECT name 
                  FROM world y
                  WHERE y.continent=x.continent);

```
8.First country of each continent (alphabetically)[Alternative]
```sql
select continent,name from world
where name in (select min(name) from world group by continent  );
```

### Difficult Questions That Utilize Techniques Not Covered In Prior Sections

9.Find the continents where all countries have a population <= 25000000. 
Then find the names of the countries associated with these continents. 
Show name, continent and population.
```sql
SELECT name, continent, population 
FROM world x
WHERE 25000000 >= ALL(SELECT population
	              FROM world y
		      WHERE x.continent = y.continent
                      AND y.population>0);
```
9.ALternative!!
```sql
SELECT name,continent,population 
FROM world
WHERE continent in (SELECT continent 
                    FROM world
                    group by continent
                    having max(population) <= 25000000);
```

10.Some countries have populations more than three times that of any of their neighbours (in the same continent). 
Give the countries and continents.
```sql
SELECT name, continent FROM world x
WHERE population >= ALL(SELECT population*3
                        FROM world y
                        WHERE x.continent = y.continent
                        AND x.name != y.name);
```
## SUM and COUNT
1.Total world population
```sql
SELECT SUM(population) as "Total Population"
FROM world;
```
2.List all the continents - just once each.
```sql
SELECT DISTINCT continent 
FROM world;
```
2.List all the continents - just once each.(Alternative)
```sql
SELECT continent 
FROM world
group by continent;
```
3.GDP of Africa
```sql
SELECT SUM(gdp) as "GDP of Africa" 
FROM world
WHERE continent = 'Africa';
```
4.Count the big countries
```sql
SELECT COUNT(name) as "Number of Countries" 
FROM world
WHERE area >= 1000000;
```
5.Baltic states population
```sql
SELECT SUM(population) as "Total Population"
FROM world
WHERE name IN ('Estonia', 'Latvia', 'Lithuania');
```
6.Counting the countries of each continent
```sql
SELECT continent, COUNT(name) "No. of countries" 
FROM world
GROUP BY continent;
```
7.Counting big countries in each continent
```sql
SELECT continent, COUNT(name) "Big countries" FROM world
WHERE population > 10000000
GROUP BY continent;
```
8.Counting big continents
```sql
SELECT continent 
FROM world
GROUP BY continent
HAVING SUM(population) > 100000000;
```
[The nobel table can be used to practice more SUM and COUNT functions.](https://sqlzoo.net/wiki/The_nobel_table_can_be_used_to_practice_more_SUM_and_COUNT_functions.)

1.Show the total number of prizes awarded.
```sql
SELECT count(winner) "Total Prizes" 
FROM nobel;
```
2.List each subject - just once
```sql
SELECT distinct(subject) 
FROM nobel;

```
3.Show the total number of prizes awarded for Physics.
```sql
SELECT COUNT(WINNER) 
FROM nobel
WHERE subject = 'Physics';
```
4.For each subject show the subject and the number of prizes.
```sql
SELECT subject, count(winner) "No. of prizes"
FROM nobel
group by subject;
```
5.For each subject show the first year that the prize was awarded.
```sql
SELECT subject,min(yr) 
FROM nobel 
group by subject;
```
6.For each subject show the number of prizes awarded in the year 2000.
```sql
SELECT subject,count(winner)  "No. of Prizes"
FROM nobel
WHERE yr = 2000
group by subject;
```
7.Show the number of different winners for each subject.
```sql
SELECT subject,count(distinct winner)  "Diff Winners"
FROM nobel
group by subject;
```
8.For each subject show how many years have had prizes awarded.
```sql
SELECT subject,count(distinct yr) "Years"
FROM nobel
group by subject;
```
9.Show the years in which three prizes were given for Physics.
```sql
SELECT yr
FROM nobel 
WHERE subject = 'Physics' 
group by yr
having count(winner) = 3;
```
10.Show winners who have won more than once.
```sql
SELECT winner
FROM nobel
group by winner
having count(winner) > 1;
```

11.Show winners who have won more than once.
```sql
SELECT winner
FROM nobel
group by winner
having count( distinct subject) > 1;
--Dont forget the distinct!!
```
12.Show the year and subject where 3 prizes were given. Show only years 2000 onwards.
```sql
SELECT yr,subject 
FROM nobel
WHERE yr >= 2000
group by yr,subject 
having count(winner) = 3;
```
## JOIN
JOIN and UEFA EURO 2012

1.Modify it to show the matchid and player name for all goals scored by Germany. 
To identify German players, check for: teamid = 'GER'
```sql
SELECT matchid,player 
FROM goal 
WHERE teamid = 'GER' ;
```

2.Show id, stadium, team1, team2 for just game 1012
```sql
SELECT id,stadium,team1,team2
FROM game
WHERE id = 1012;
```

3.Modify it to show the player, teamid, stadium and mdate for every German goal.
```sql
SELECT player,teamid,stadium,mdate
FROM game 
JOIN goal ON (id=matchid)
WHERE teamid = 'GER';
```
3.The above query can be  written across diffrent servers as follows:
```sql
SELECT go.player,go.teamid,gm.stadium,gm.mdate
FROM game gm
JOIN goal go ON (gm.id=go.matchid)
WHERE go.teamid = 'GER';
```

4.Show the team1, team2 and player for every goal scored by a player called Mario.
Use player LIKE 'Mario%'
```sql
SELECT team1,team2,player
FROM game
JOIN goal on (id=matchid)
WHERE player LIKE 'Mario%';
```
5.Show player, teamid, coach, gtime for all goals scored in the first 10 minutes gtime<=10
```sql
SELECT player, teamid,coach, gtime
FROM goal 
JOIN eteam on (teamid = id)
WHERE gtime<=10;
```
6.List the dates of the matches and the name of the team in which 'Fernando Santos' was the team1 coach.
```sql
SELECT mdate,teamname
FROM game
JOIN eteam on (team1=eteam.id)
WHERE coach = 'Fernando Santos';
--Notice that (team1=eteam.id) was used to join,as both 'game' and 'eteam' had columns named 'id';
```
7.List the player for every goal scored in a game where the stadium was 'National Stadium, Warsaw'
```sql
SELECT player
FROM goal 
JOIN game on (matchid = id)
WHERE stadium = 'National Stadium, Warsaw';
```
### More Difficult Questions....
8.Show the name of all players who scored a goal against Germany.
```sql
SELECT player
FROM game JOIN goal ON matchid = id 
WHERE (team1='GER' OR team2='GER')
AND TEAMID != 'GER';
```
9.Show teamname and the total number of goals scored.
```sql
SELECT teamname,count(teamid) "goals"
FROM goal
JOIN eteam on (teamid = id)
group by teamname;
```
10.Show the stadium and the number of goals scored in each stadium.
```sql
SELECT stadium,count(gtime) "goals"
FROM goal
JOIN game on (matchid = id)
group by stadium;
```
11.For every match involving 'POL', show the matchid, date and the number of goals scored.
```sql
SELECT matchid,mdate,count(gtime) "goals"
FROM game JOIN goal ON matchid = id 
WHERE (team1 = 'POL' OR team2 = 'POL')
group by matchid,mdate;
```
12.For every match where 'GER' scored, show matchid, match date and the number of goals scored by 'GER'
```sql
SELECT matchid,mdate,count(teamid) 
FROM game
JOIN goal on (matchid=id)
WHERE teamid = 'GER'
group by matchid,mdate;
```
13.List every match with the goals scored by each team as shown. This will use "CASE WHEN" which has not been explained in any previous exercises.
```sql
SELECT mdate,
team1,
sum(CASE WHEN teamid=team1 THEN 1 ELSE 0 END) score1,
team2,
sum(CASE WHEN teamid=team2 THEN 1 ELSE 0 END) score2
FROM game 
LEFT JOIN goal ON matchid = id
group by matchid, mdate,team1,team2
order by mdate, matchid, team1 and team2;
--Don't forget the 'LEFT JOIN'!!This helps you select all the data in the table 1(here game),corresponding to which there may or may not be data in table 2(here goal)
```

[Old JOIN Tutorial](https://sqlzoo.net/wiki/Old_JOIN_Tutorial)

1.Show the athelete (who) and the country name for medal winners in 2000.
```sql
SELECT who,name
FROM ttms
JOIN country on (country = id)
WHERE games = 2000;
```
2.Show the who and the color of the medal for the medal winners from 'Sweden'.
```sql
SELECT who,color 
FROM ttms
JOIN country on (country = id)
WHERE name = 'Sweden';
```
3.Show the years in which 'China' won a 'gold' medal.
```sql
SELECT games 
FROM ttms
JOIN country on (country = id)
WHERE name = 'China'
AND color = 'gold';
```
Women's Singles Table Tennis Olympics Database.

4.Show who won medals in the 'Barcelona' games.
```sql
SELECT who
FROM ttws 
JOIN games ON (ttws.games=games.yr)
WHERE city = 'Barcelona';
```
5.Show which city 'Jing Chen' won medals. Show the city and the medal color.
```sql
SELECT city,color
FROM ttws
JOIN games on (ttws.games = games.yr)
WHERE who = 'Jing Chen';
```
6.Show who won the gold medal and the city.
```sql
SELECT who,city
FROM ttws
JOIN games ON (ttws.games = games.yr)
WHERE color = 'gold';
```
Table Tennis Mens Doubles.

7.Show the games and color of the medal won by the team that includes 'Yan Sen'.
```sql
SELECT games,color 
FROM ttmd
JOIN team on (ttmd.team = team.id)
WHERE name = 'Yan Sen';
```
8.Show the 'gold' medal winners in 2004.
```sql
SELECT name
FROM ttmd
JOIN team on (ttmd.team = team.id)
WHERE color = 'gold' 
AND games =2004;
```
9.Show the name of each medal winner country 'FRA'.
```sql
SELECT name 
FROM ttmd 
JOIN team on ttmd.team = team.id
WHERE country =  'FRA';
```

## More JOIN

1.List the films where the yr is 1962 (Show id, title)
```sql
SELECT id, title
FROM movie
WHERE yr=1962;
```
2.When was Citizen Kane released?
```sql
SELECT yr
FROM movie
WHERE title = 'Citizen Kane';
```
3.Star Trek movies
```sql
SELECT id,title,yr
FROM movie
WHERE title LIKE '%Star Trek%'
order by yr;
```
4.id for actor Glenn Close
```sql
SELECT id
FROM actor
WHERE name = 'Glenn Close';
```
5.id for Casablanca
```sql
SELECT id 
FROM movie
WHERE title = 'Casablanca';

```
6.Cast list for Casablanca
```sql
SELECT name 
FROM actor
JOIN casting on casting.actorid = actor.id
JOIN movie on movie.id = casting.movieid
WHERE movie.title = 'Casablanca' ;
--You could use 'movie.id = 11768' and get the above results!!
```
7.Alien cast list
```sql
SELECT name
FROM actor
JOIN casting on casting.actorid = actor.id
JOIN movie on movie.id = casting.movieid
WHERE title =  'Alien';
```
8.Harrison Ford movies
```sql
SELECT title
FROM movie
JOIN casting on casting.movieid = movie.id
JOIN actor on actor.id = casting.actorid
WHERE actor.name = 'Harrison Ford';
```
9.Harrison Ford as a supporting actor
```sql
SELECT title
FROM movie
JOIN casting on casting.movieid = movie.id
JOIN actor on actor.id = casting.actorid
WHERE casting.ord != 1
AND actor.name = 'Harrison Ford' ;
--You could use 'casting.ord > 1' and get the same result!
```
10.Lead actors in 1962 movies
```sql
SELECT title,actor.name
FROM movie
JOIN casting on casting.movieid = movie.id
JOIN actor on casting.actorid = actor.id
WHERE yr = 1962
AND casting.ord = 1;
```
### Harder Questions

11.Busy years for Rock Hudson
```sql
SELECT yr,COUNT(title) "No. of movies"
FROM movie 
JOIN casting ON movie.id=movieid
JOIN actor   ON actorid=actor.id
WHERE actor.name='Rock Hudson'
GROUP BY yr
HAVING COUNT(title) > 2;
```
12.Lead actor in Julie Andrews movies
```sql
SELECT title,name
FROM movie 
JOIN casting on (casting.movieid = movie.id)
JOIN actor on (actor.id = casting.actorid)
WHERE movie.id IN (SELECT movieid 
                   FROM casting
                   WHERE actorid IN (SELECT id 
                                     FROM actor 
                                     WHERE name = 'Julie Andrews'))
AND casting.ord = 1;
```
13.Actors with 15 leading roles
```sql
SELECT actor.name 
FROM casting
JOIN actor on (actor.id = casting.actorid)
WHERE ord = 1
group by actor.name
having count(ord) >=15 
order by actor.name;
```
14.List the films released in the year 1978 ordered by the number of actors in the cast, then by title.
```sql
SELECT title,count(ord) "total casting"
FROM movie
JOIN casting on casting.movieid = movie.id
WHERE yr = 1978
group by title
order by count(ord) desc, title;

```
15.List all the people who have worked with 'Art Garfunkel'.
```sql
SELECT actor.name
FROM actor
JOIN casting on casting.actorid = actor.id
JOIN movie on movie.id = casting.movieid
WHERE movie.id IN (SELECT movieid 
                   FROM casting 
                   WHERE actorid IN (SELECT id 
                                     FROM actor
                                     WHERE name ='Art Garfunkel' ))
AND actor.name !='Art Garfunkel';
--the catch is the And statement in the last line!! 
```

## Using NULL
1.NULL, INNER JOIN, LEFT JOIN, RIGHT JOIN
```sql
SELECT name 
FROM teacher
WHERE dept is NULL;
```
2.Note the INNER JOIN misses the teachers with no department and the departments with no teacher.
```sql
SELECT teacher.name, dept.name
FROM teacher  
INNER JOIN dept on (teacher.dept=dept.id);
```
3.Use a different JOIN so that all teachers are listed.
```sql
SELECT teacher.name,dept.name
FROM teacher 
LEFT JOIN dept on (teacher.dept=dept.id);

```
4.Use a different JOIN so that all departments are listed.
```sql
SELECT teacher.name,dept.name
FROM teacher
RIGHT JOIN dept on (teacher.dept = dept.id);
```
5.Using the COALESCE function
```sql
SELECT name,COALESCE(mobile, '07986 444 2266') as "mobile"
FROM teacher;
```
6.Use the COALESCE function and a LEFT JOIN to print the teacher name and department name.
Use the string 'None' where there is no department.
```sql
SELECT teacher.name,COALESCE(dept.name,'None') as "dept"
FROM teacher
LEFT JOIN dept on (teacher.dept=dept.id);
```
7.Use COUNT to show the number of teachers and the number of mobile phones.
```sql
SELECT COUNT(name) "no. of teachers",count(mobile) "no.of mobile"
FROM teacher;
```
8.Use COUNT and GROUP BY dept.name to show each department and the number of staff.
Use a RIGHT JOIN to ensure that the Engineering department is listed.
```sql
SELECT dept.name,count(teacher.name)
FROM teacher
RIGHT JOIN dept on (teacher.dept=dept.id)
group by dept.name;
```
9.Using CASE
```sql
SELECT teacher.name,
case WHEN dept = 1 or dept = 2 THEN 'Sci'
     ELSE 'Art' 
     END as dept
FROM teacher;
```
10.Use CASE to show the name of each teacher followed by 'Sci' if the teacher is in dept 1 or 2, show 'Art' if the teacher's dept is 3 and 'None' otherwise.
```sql
SELECT name,
CASE WHEN dept = 1 or dept = 2 THEN 'Sci'
     WHEN dept = 3 THEN 'Art'
     ELSE 'None'
     END as dept
FROM teacher;
```
## Self JOIN
Edinburgh Buses

1.How many stops are in the database.
```sql
SELECT DISTINCT COUNT(*) 
FROM stops;
```
2.Find the id value for the stop 'Craiglockhart'
```sql
SELECT id 
FROM stops
WHERE name = 'Craiglockhart';
```
3.Give the id and the name for the stops on the '4' 'LRT' service.
```sql 
SELECT stops.id,stops.name
FROM route
LEFT JOIN stops on (stops.id = route.stop)
WHERE num = 4
order by company;
```
4.Routes and stops
```sql
SELECT company, num, COUNT(*) "no of stops"
FROM route WHERE stop=149 OR stop=53
GROUP BY company, num
having COUNT(*) = 2;
```
5.
```sql
SELECT a.company, a.num, a.stop, b.stop
FROM route a 
JOIN route b on (a.company=b.company AND a.num=b.num)
WHERE a.stop=53
AND b.stop = (SELECT id 
               FROM stops 
               WHERE name = 'London Road');
```
6.
```sql
SELECT a.company, a.num, stopa.name, stopb.name
FROM route a
JOIN route b ON (a.company=b.company AND a.num=b.num)
JOIN stops stopa ON (a.stop=stopa.id)
JOIN stops stopb ON (b.stop=stopb.id)
WHERE stopa.name='Craiglockhart'
AND stopb.name = 'London Road';
```
7.
```sql
SELECT a.company, a.num  
FROM route a, route b
WHERE a.num = b.num AND (a.stop = 115 AND b.stop = 137)
group by a.company, a.num;
```
8.
```sql
SELECT a.company,b.num
FROM route a ,route b
WHERE a.num = b.num and (a.stop = (SELECT id 
                                   FROM stops 
                                   WHERE name='Craiglockhart')  
                         AND b.stop = (SELECT id 
                                       FROM stops 
                                       WHERE name='Tollcross'))
group by a.company,b.num;
```
9.
```sql
SELECT name,a.company,a.num
FROM route a 
JOIN route b on (a.num = b.num AND b.company = a.company)
JOIN stops on a.stop = stops.id
WHERE b.stop = 53;
```
10.
```sql
SELECT a.num, a.company, stopb.name, c.num, c.company
FROM route a
JOIN route b ON (a.company = b.company AND a.num = b.num)
JOIN (route c JOIN route d ON (c.company = d.company AND c.num = d.num))
JOIN stops stopa ON a.stop = stopa.id
JOIN stops stopb ON b.stop = stopb.id
JOIN stops stopc ON c.stop = stopc.id
JOIN stops stopd ON d.stop = stopd.id
WHERE stopa.name = 'Craiglockhart'
AND stopd.name = 'Sighthill'
AND stopb.name = stopc.name
ORDER BY LENGTH(a.num), b.num, stopb.name, LENGTH(c.num), d.num;
```
