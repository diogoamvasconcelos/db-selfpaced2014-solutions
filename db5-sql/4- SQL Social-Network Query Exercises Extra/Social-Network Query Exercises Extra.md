# db5-sql SQL Social-Network Query Exercises Extra

##Exercises Intro:
Students at your hometown high school have decided to organize their social network using databases. So far, they have collected information about sixteen students in four grades, 9-12. Here's the schema: 

Highschooler ( ID, name, grade ) 
English: There is a high school student with unique ID and a given first name in a certain grade. 

Friend ( ID1, ID2 ) 
English: The student with ID1 is friends with the student with ID2. Friendship is mutual, so if (123, 456) is in the Friend table, so is (456, 123). 

Likes ( ID1, ID2 ) 
English: The student with ID1 likes the student with ID2. Liking someone is not necessarily mutual, 	so if (123, 456) is in the Likes table, there is no guarantee that (456, 123) is also present. 

Your queries will run over a small data set conforming to the schema.
[View the database](https://lagunita.stanford.edu/c4x/DB/SQL/asset/socialdata.html)

**Note:** Your queries are executed using SQLite, so you must conform to the SQL constructs supported by SQLite.


## Q1 - For every situation where student A likes student B, but student B likes a different student C, return the names and grades of A, B, and C.

select H2.name
select H1.name, H1.grade, H2.name, H2.grade, H3.name, H3.grade
from Highschooler H1, Highschooler H2, Likes L1, Highschooler H3
where H1.ID = L1.ID1 and H2.ID = L1.ID2
and not exists (select * from Likes L2 where L2.ID1 = H2.ID and L2.ID2 = H1.ID)
and exists (select * from Likes L2 where L2.ID1 = H2.ID and L2.ID2 = H3.ID );

## Q2 - Find those students for whom all of their friends are in different grades from themselves. Return the students' names and grades.

select distinct H1.name, H1.grade
from Highschooler H1, Friend F1
where F1.ID1 = H1.ID
and not exists (select * 
				from Highschooler H2, Friend F2 
				where F2.ID1 = H1.ID 
                and F2.ID2 = H2.ID
                and H1.grade == H2.grade);

## Q3 - What is the average number of friends per student? (Your result should be just one number.)

select avg(numberFriends)
from    (select ID1, count(*) as numberFriends
        from Friend
        group by (ID1));

## Q4 - Find the number of students who are either friends with Cassandra or are friends of friends of Cassandra. Do not count Cassandra, even though technically she is a friend of a friend.

select 
(select count(*)
from    (select ID2 as FriendID
        from HighSchooler H1, Friend F1
        where F1.ID1 = H1.ID and H1.name = "Cassandra"), Friend F2, HighSchooler H2
where H2.name = "Cassandra" and F2.ID2 <> H2.ID and F2.ID1 = FriendID)
+
(select count(*)
from HighSchooler H1, Friend F1
where F1.ID1 = H1.ID and H1.name = "Cassandra");

## Q5 - Find the name and grade of the student(s) with the greatest number of friends.

select name, grade
from (select ID1 as ID, count(*)as numberFriend 
    from Friend 
    group by ID1 
    having numberFriend = (select max(numberFriend) as maxNumFriend
                            from (select ID1, count(*) as numberFriend from Friend group by ID1)))
    join HighSchooler using(ID);