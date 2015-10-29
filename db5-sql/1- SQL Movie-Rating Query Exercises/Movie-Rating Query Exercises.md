# db5-sql SQL Movie-Rating Query Exercises

##Exercises Intro:
You've started a new movie-rating website, and you've been collecting data on reviewers' ratings of various movies. There's not much data yet, but you can still try out some interesting queries. Here's the schema: 

Movie ( mID, title, year, director ) 
English: There is a movie with ID number mID, a title, a release year, and a director. 

Reviewer ( rID, name ) 
English: The reviewer with ID number rID has a certain name. 

Rating ( rID, mID, stars, ratingDate ) 
English: The reviewer rID gave the movie mID a number of stars rating (1-5) on a certain ratingDate. 

Your queries will run over a small data set conforming to the schema.
[View the database](https://lagunita.stanford.edu/c4x/DB/SQL/asset/moviedata.html)

**Note:** Your queries are executed using SQLite, so you must conform to the SQL constructs supported by SQLite.


## Q1 - Find the titles of all movies directed by Steven Spielberg.

select title
from Movie
where director = "Steven Spielberg";

## Q2 - Find all years that have a movie that received a rating of 4 or 5, and sort them in increasing order.

select distinct year
from Movie join Rating using (mID)
where stars >= 4
order by year;

## Q3 - Find the titles of all movies that have no ratings.

select title
from Movie
where mID not in (select mID from Rating);

## Q4 - Some reviewers didn't provide a date with their rating. Find the names of all reviewers who have ratings with a NULL value for the date.

select distinct name
from Reviewer join Rating using(rID)
where ratingDate is null;

## Q5 - Write a query to return the ratings data in a more readable format: reviewer name, movie title, stars, and ratingDate. Also, sort the data, first by reviewer name, then by movie title, and lastly by number of stars.

select name, title, stars, ratingDate
from (Reviewer join Rating using(rID)) join Movie using(mID)
order by name, title, stars; 

## Q6 - For all cases where the same reviewer rated the same movie twice and gave it a higher rating the second time, return the reviewer's name and the title of the movie.

select name, title
from ((	select R1.mID, R1.rID
		from Rating R1, Rating R2
		where R1.rID = R2.rID and R1.mID = R2.mID 
		and R1.ratingDate > R2.ratingDate and R1.stars > R2.stars)
		join Movie using(mID)) join Reviewer using(rID);

## Q7 - For each movie that has at least one rating, find the highest number of stars that movie received. Return the movie title and number of stars. Sort by movie title.

select title, max(stars) as maxStars
from Rating join Movie using(mID)
group by mID
order by title;

## Q8 - For each movie, return the title and the 'rating spread', that is, the difference between highest and lowest ratings given to that movie. Sort by rating spread from highest to lowest, then by movie title.

select title, max(stars) - min(stars) as starSpread
from Rating join Movie using (mID)
group by mID
order by starSpread desc, title;

## Q9 - Find the difference between the average rating of movies released before 1980 and the average rating of movies released after 1980. (Make sure to calculate the average rating for each movie, then the average of those averages for movies before 1980 and movies after. Don't just calculate the overall average rating before and after 1980.)

select abs((select avg(starAvg)
from (select mID, avg(stars) as starAvg, year from Rating join Movie using(mID) group by mID)
where year < 1980)
-
(select avg(starAvg)
from (select mID, avg(stars) as starAvg, year from Rating join Movie using(mID) group by mID)
where year >= 1980));