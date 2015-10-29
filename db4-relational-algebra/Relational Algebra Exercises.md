# db4-relational-albegra-exercises

### Exercises Intro:
In this assignment you are to write relational algebra queries over a small database, executed using our RA Workbench. Behind the scenes, the RA workbench translates relational algebra expressions into SQL queries over the database stored in SQLite. Since relational algebra symbols aren't readily available on most keyboards, RA uses a special syntax described in our RA [Relational Algebra Syntax guide](https://users.cs.duke.edu/~junyang/ra/).

We've created a small sample database to use for this assignment. It contains four relations:

    - Person(name, age, gender)       // name is a key
    - Frequents(name, pizzeria)       // [name,pizzeria] is a key
    - Eats(name, pizza)               // [name,pizza] is a key
    - Serves(pizzeria, pizza, price)  // [pizzeria,pizza] is a key

 [View the database](https://lagunita.stanford.edu/c4x/DB/RA/asset/pizzadata.html)

### Q1 - Find all pizzas eaten by at least one female over the age of 20. 
	\project_{pizza} (\select_{gender = "female" and age > 20} Person \join Eats);

### Q2 - Find the names of all females who eat at least one pizza served by Straw Hat. (Note: The pizza need not be eaten at Straw Hat.)

	\project_{name} ((\select_{pizzeria = "Straw Hat"} Serves) \join (\select_{gender = "female"} Person \join Eats));

### Q3 - Find all pizzerias that serve at least one pizza for less than $10 that either Amy or Fay (or both) eat.

	\project_{pizzeria} ((\select_{price < 10} Serves) \join (\select_{name = "Amy" or name = "Fay"} Person \join Eats));

### Q4 - Find all pizzerias that serve at least one pizza for less than $10 that both Amy and Fay eat.

	\project_{pizzeria} ((\select_{price < 10} Serves) \join ((\project_{pizza} (\select_{name = "Amy"} Person \join Eats)) \intersect (\project_{pizza} (\select_{name = "Fay"} Person \join Eats))));

### Q5 - Find the names of all people who eat at least one pizza served by Dominos but who do not frequent Dominos.

(\project_{name} (Eats \join (\project_{pizza} (\select_{pizzeria = "Dominos"} Serves)))) \diff (\project_{name} (\select_{pizzeria = "Dominos"} Frequents));

### Q6 - Find all pizzas that are eaten only by people younger than 24, or that cost less than $10 everywhere they're served.

(	(\project_{pizza} Eats) \diff (\project_{pizza} ((\select_{age >= 24} Person) \join Eats)))  \union ((\project_{pizza} (\select_{price < 10} Serves)) \diff (\project_{pizza} (\select_{price >= 10} Serves)));

### Q7 - Find the age of the oldest person (or people) who eat mushroom pizza. 
(This query is quite challenging; congratulations if you get it right.) 

 	(\project_{age} (\select_{pizza = "mushroom"} (Person \join Eats))) \diff (\project_{age} ((\project_{age} (\select_{pizza = "mushroom"} (Person \join Eats))) \join_{age < age2} (\rename_{age2} (\project_{age} (\select_{pizza = "mushroom"} (Person \join Eats))))));

 ### Q8 - Find all pizzerias that serve only pizzas eaten by people over 30. 
(This query is quite challenging; congratulations if you get it right.)

	(\project_{pizzeria} ((Serves) \join (\project_{pizza} ((\select_{age > 30} Person) \join Eats)))) \diff (\project_{pizzeria} ((Serves \join ((\project_{pizza} Eats) \diff (\project_{pizza} ((\select_{age > 30} Person) \join Eats))))));

### Q9 - Find all pizzerias that serve every pizza eaten by people over 30. 
(This query is very challenging; extra congratulations if you get it right.)

	(\project_{pizzeria} ((Serves) \join (\project_{pizza} ((\select_{age > 30} Person) \join Eats)))) \diff (\project_{pizzeria} (((\project_{pizzeria} Serves) \cross (\project_{pizza} ((\select_{age > 30} Person) \join Eats))) \diff (\project_{pizzeria, pizza} ((Serves) \join (\project_{pizza} ((\select_{age > 30} Person) \join Eats))))))
