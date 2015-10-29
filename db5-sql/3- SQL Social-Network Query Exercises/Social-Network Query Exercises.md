# db5-sql SQL Social-Network Query Exercises

##Exercises Intro:
Students at your hometown high school have decided to organize their social network using databases. So far, they have collected information about sixteen students in four grades, 9-12. Here's the schema: 

Highschooler ( ID, name, grade ) 
English: There is a high school student with unique ID and a given first name in a certain grade. 

Friend ( ID1, ID2 ) 
English: The student with ID1 is friends with the student with ID2. Friendship is mutual, so if (123, 456) is in the Friend table, so is (456, 123). 

Likes ( ID1, ID2 ) 
English: The student with ID1 likes the student with ID2. Liking someone is not necessarily mutual, so if (123, 456) is in the Likes table, there is no guarantee that (456, 123) is also present. 

Your queries will run over a small data set conforming to the schema.
[View the database](https://lagunita.stanford.edu/c4x/DB/SQL/asset/socialdata.html)

**Note:** Your queries are executed using SQLite, so you must conform to the SQL constructs supported by SQLite.


## Q1 - Find the names of all students who are friends with someone named Gabriel.

select H2.name
from Highschooler H1, Highschooler H2, Friend
where H1.ID = ID1 and H1.name = "Gabriel" and H2.ID = ID2;

## Q2 - For every student who likes someone 2 or more grades younger than themselves, return that student's name and grade, and the name and grade of the student they like.

select name1, grade1, name as name2, grade as grade2
from   (select name as name1, grade as grade1, ID2
        from (select ID1, ID2
             from Likes
             where 2 <= (select grade from Highschooler where ID = ID1) -
             (select grade from Highschooler where ID = ID2))
             join Highschooler on ID1 = ID)
        join Highschooler on ID2 = ID;

## Q3 - For every pair of students who both like each other, return the name and grade of both students. Include each pair only once, with the two names in alphabetical order.

select H1.name, H1.grade, H2.name, H2.grade
from Highschooler H1, Highschooler H2, Likes L1
where H1.ID = L1.ID1 and H2.ID = L1.ID2 and H1.name < H2.name 
and exists (select * from Likes L2 where L2.ID1 = L1.ID2 and L2.ID2 = L1.ID1);

## Q4 - Find all students who do not appear in the Likes table (as a student who likes or is liked) and return their names and grades. Sort by grade, then by name within each grade.

select name, grade
from Highschooler
where ID not in (select ID1 from Likes) and ID not in (select ID2 from Likes)
order by grade, name;

## Q5 - For every situation where student A likes student B, but we have no information about whom B likes (that is, B does not appear as an ID1 in the Likes table), return A and B's names and grades.

select H1.name, H1.grade, H2.name, H2.grade
from Highschooler H1, Highschooler H2, Likes L1
where H1.ID = L1.ID1 and H2.ID = L1.ID2
and L1.ID2 not in (select ID1 from Likes);

## Q6 - Find names and grades of students who only have friends in the same grade. Return the result sorted by grade, then by name within each grade.

select distinct H1.name, H1.grade
from Highschooler H1, Friend
where H1.ID = ID1 and  not exists (select *
                              from Highschooler H2, Friend
                              where H1.ID = ID1 and H2.ID = ID2 and H1.grade <> H2.grade)
order by grade, name;

## Q7 - For each student A who likes a student B where the two are not friends, find if they have a friend C in common (who can introduce them!). For all such trios, return the name and grade of A, B, and C.

select H1.name, H1.grade, H2.name, H2.grade, H3.name, H3.grade
from Highschooler H1, Highschooler H2, Likes L1, Highschooler H3
where H1.ID = L1.ID1 and H2.ID = L1.ID2
and not exists (select * from Friend F where L1.ID1 = F.ID1 and L1.ID2 = F.ID2)
and exists (select * from Friend F where H1.ID = F.ID1 and H3.ID = F.ID2)
and exists (select * from Friend F where H2.ID = F.ID1 and H3.ID = F.ID2)

## Q8 - Find the difference between the number of students in the school and the number of different first names.

select (select count(name) from Highschooler) - (select count(distinct name) from Highschooler)

## Q9 - Find the name and grade of all students who are liked by more than one other student.

select name, grade
from    (select ID2, count(ID2)
        from Likes
        group by ID2
        having count(ID2) > 1), Highschooler
 where ID = ID2