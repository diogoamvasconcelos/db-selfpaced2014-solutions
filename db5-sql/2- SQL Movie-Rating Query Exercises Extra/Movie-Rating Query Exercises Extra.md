# db5-sql SQL Movie-Rating Query Exercises Extra

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


## Q1 - Find the names of all reviewers who rated Gone with the Wind.

select distinct name
from (Reviewer join Rating using(rID)) join Movie using(mID)
where title = "Gone with the Wind";

## Q2 - For any rating where the reviewer is the same as the director of the movie, return the reviewer name, movie title, and number of stars.

select name, title, stars
from (Reviewer join Rating using(rID)) join Movie using(mID)
where name = director;

## Q3 - Return all reviewer names and movie names together in a single list, alphabetized. (Sorting by the first name of the reviewer and first word in the title is fine; no need for special processing on last names or removing "The".)

select name from Reviewer
union
select title as name from Movie
order by name;

## Q4 - Find the titles of all movies not reviewed by Chris Jackson.

select distinct title
from Movie as table1
where not exists (	select * 
                	from (Rating join Reviewer using(rID)) as table2
                	where name = "Chris Jackson" and table1.mID = table2.mID);

## Q5 - For all pairs of reviewers such that both reviewers gave a rating to the same movie, return the names of both reviewers. Eliminate duplicates, don't pair reviewers with themselves, and include each pair only once. For each pair, return the names in the pair in alphabetical order.

select distinct R1.name, R2.name
from (Reviewer join Rating using(rID)) R1, (Reviewer join Rating using(rID)) R2
where R1.name < R2.name and R1.mID = R2.mID

## Q6 - For each rating that is the lowest (fewest stars) currently in the database, return the reviewer name, movie title, and number of stars.

select name, title, stars
from (Rating join Movie using(mID)) join Reviewer using(rID)
where stars = (select min(stars) from Rating);

## Q7 - List movie titles and average ratings, from highest-rated to lowest-rated. If two or more movies have the same average rating, list them in alphabetical order.

select title, avg(stars) as avgRating
from Rating join Movie using(mID)
group by mID
order by avgRating desc, title;

## Q8 - Find the names of all reviewers who have contributed three or more ratings. (As an extra challenge, try writing the query without HAVING or without COUNT.)

select name
from    (select count(*) as numberOfRating, rID
        from Rating
        group by rID) join Reviewer using(rID)
where numberOfRating >= 3;

## Q9 - Some directors directed more than one movie. For all such directors, return the titles of all movies directed by them, along with the director name. Sort by director name, then movie title. (As an extra challenge, try writing the query both with and without COUNT.)

select title, director
from   (select M1.director
        from Movie M1, Movie M2
        where M1.director = M2.director and M1.title < M2.title)
        join Movie using(director)
order by director, title;