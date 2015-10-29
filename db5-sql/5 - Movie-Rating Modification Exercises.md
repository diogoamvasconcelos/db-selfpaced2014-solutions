# db5-sql SQL Movie-Rating Modification Exercises

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


## Q1 - Add the reviewer Roger Ebert to your database, with an rID of 209.

insert into Reviewer values (209, "Roger Ebert");

## Q2 - Insert 5-star ratings by James Cameron for all movies in the database. Leave the review date as NULL.

insert into Rating
select rID, mID, 5, NULL
from Movie, Reviewer
where name = "James Cameron";

## Q3 - For all movies that have an average rating of 4 stars or higher, add 25 to the release year. (Update the existing tuples; don't insert new tuples.)

update Movie
set year = year + 25
where 4 <= (select avg(stars) from Rating R where R.mID = Movie.mID)

## Q4 - Remove all ratings where the movie's year is before 1970 or after 2000, and the rating is fewer than 4 stars.

delete from Rating
where mID in 
(select mID from Movie where year < 1970 or year > 2000)
and stars < 4;