# db5-sql SQL Social-Network Modification Exercises

### Exercises Intro:
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


### Q1 - It's time for the seniors to graduate. Remove all 12th graders from Highschooler.

	delete from Highschooler
	where grade = 12;

### Q2 - If two students A and B are friends, and A likes B but not vice-versa, remove the Likes tuple.

	delete from Likes
	where exists (select * from Friend F where F.ID1 = Likes.ID1 and F.ID2 = Likes.ID2)
	and not exists (select * from Likes L2 where L2.ID1 = Likes.ID2 and L2.ID2 = Likes.ID1);

### Q3 - For all cases where A is friends with B, and B is friends with C, add a new friendship for the pair A and C. Do not add duplicate friendships, friendships that already exist, or friendships with oneself. (This one is a bit challenging; congratulations if you get it right.)

	insert into Friend
	select distinct F1.ID1, F2.ID2
	from Friend F1, Friend F2
	where F1.ID2 = F2.ID1 and F1.ID1 <> F2.ID2
	and F2.ID2 not in (select F3.ID2 from Friend F3 where F3.ID1 = F1.ID1);