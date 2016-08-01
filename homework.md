## SQL Questions

First create a database called fringe_shows
```
  #terminal
  psql
  create database fringe_shows;
  \q
```

Populate the data using the script - fringeshows.sql
```
  #terminal
  psql -d fringe_shows -f fringeshows.sql
```

Using the SQL Database file given to you as the source of data to answer the questions.  Copy the SQL command you have used to get the answer, and paste it below the question, along with the result they gave.


## Section 1

  Revision of concepts that we've learnt in SQL today

  1. Select the names of all users.
  ```
  # SELECT name FROM users;
         name       
  ------------------
   Rick Henri
   Jay Chetty
   Keith Douglas
   Ashleigh Adams
   Euan Blackledge
   Chris Flint
   Nico di Lillo
   Joe Maher
   Marie Moyles
   Iain Stewart
   Megan Strachan
   Russell Williams
   Sam Werngren
   Natalie Simpson
   Davide de Lerma
   Josh Kearns
   Renwick Drysdale
   Brian Morrice
  (18 rows)
  ```

  2. Select the names of all shows that cost less than £15.
  ```
  # SELECT name FROM shows WHERE price < 15;
               name             
  ------------------------------
   Le Haggis
   Paul Dabek Mischief 
   Best of Burlesque
   Two become One
   Urinetown
   Two girls, one cup of comedy
  (6 rows)
  ```

  3. Insert a user with the name "Val Gibson" into the users table.
  ```
  # INSERT INTO users (name) VALUES ('Val Gibson');
  INSERT 0 1
  ```

  4. Insert a record that Val Gibson wants to attend the show "Two girls, one cup of comedy".
  ```
  # SELECT id FROM users WHERE name = 'Val Gibson';
  -- Gets Val's 'id' in 'users'

   id 
  ----
   19

  # SELECT id FROM shows WHERE name = 'Two girls, one cup of comedy';
  -- Gets show's 'id' in 'shows'

   id 
  ----
   12

  # INSERT INTO shows_users (show_id, user_id) VALUES (12, 19);
  INSERT 0 1

  -- There *must* be a quicker way of doing this...
  ```

  5. Updates the name of the "Val Gibson" user to be "Valerie Gibson".
  ```
  # UPDATE users SET name = 'Valerie Gibson' WHERE name = 'Val Gibson';
  UPDATE 1
  ```

  6. Deletes the user with the name 'Valerie Gibson'.
  ```
  # DELETE FROM users WHERE name = 'Valerie Gibson';
  DELETE 1
  ```

  7. Deletes the shows for the user you just deleted.
  ```
  # DELETE FROM shows_users WHERE user_id = 19;
  DELETE 1
  ```

## Section 2

  This section involves more complex queries.  You will need to go and find out about aggregate funcions in SQL to answer some of the next questions.

  9. Select the names and prices of all shows, ordered by price in ascending order.
  ```
  # SELECT name, price FROM shows ORDER BY price ASC;

                    name                   | price 
  -----------------------------------------+-------
   Two girls, one cup of comedy            |  6.00
   Best of Burlesque                       |  7.99
   Two become One                          |  8.50
   Urinetown                               |  8.50
   Paul Dabek Mischief                     | 12.99
   Le Haggis                               | 12.99
   Joe Stilgoe: Songs on Film – The Sequel | 16.50
   Game of Thrones - The Musical           | 16.50
   Shitfaced Shakespeare                   | 16.50
   Aaabeduation – A Magic Show             | 17.99
   Camille O'Sullivan                      | 17.99
   Balletronics                            | 32.00
   Edinburgh Royal Tattoo                  | 32.99
  (13 rows)
  ```

  10. Select the average price of all shows.
  ```
  # SELECT AVG(price) FROM shows;
           avg         
  ---------------------
   15.9569230769230769
  (1 row)
  ```

  11. Select the price of the least expensive show.
  ```
  # SELECT MIN(price) FROM shows;
   min  
  ------
   6.00
  (1 row)
  ```

  12. Select the sum of the price of all shows.
  ```
  # SELECT SUM(price) FROM shows;
    sum   
  --------
   207.44
  (1 row)
  ```

  13. Select the sum of the price of all shows whose prices is less than £20.
  ```
  # SELECT SUM(price) FROM shows WHERE price < 20;
    sum   
  --------
   142.45
  (1 row)
  ```

  14. Select the name and price of the most expensive show.
  ```
  # SELECT name, price FROM shows WHERE price = (SELECT MAX(price) FROM shows);
            name          | price 
  ------------------------+-------
   Edinburgh Royal Tattoo | 32.99
  (1 row)
  ```

  15. Select the name and price of the second from cheapest show.
  ```
  # SELECT name, price FROM shows
  WHERE price = (SELECT MAX(price) FROM shows
  WHERE price < (SELECT MAX(price) FROM shows));

       name     | price 
  --------------+-------
   Balletronics | 32.00
  (1 row)
  ```

  16. Select the names of all users whose names start with the letter "N".
  ```
  # SELECT name FROM users WHERE name LIKE 'N%';
        name       
  -----------------
   Nico di Lillo
   Natalie Simpson
  (2 rows)
  ```

  17. Select the names of users whose names contain "er".
  ```
  # SELECT name FROM users WHERE name LIKE '%er%';
        name       
  -----------------
   Joe Maher
   Sam Werngren
   Davide de Lerma
  (3 rows)
  ```


## Section 3

  The following questions can be answered by using nested SQL statements but ideally you should learn about JOIN clauses to answer them.

  18. Select the time for the Edinburgh Royal Tattoo.
  ```
  # SELECT time FROM times
  INNER JOIN shows ON times.show_id = shows.id
  WHERE shows.name = 'Edinburgh Royal Tattoo';
            name          | time  
  ------------------------+-------
   Edinburgh Royal Tattoo | 22:00
  (1 row)
  ```

  19. Select the number of users who want to see "Shitfaced Shakespeare".
  ```
  # SELECT COUNT(users.name) FROM users
  INNER JOIN shows_users ON users.id = shows_users.user_id
  INNER JOIN shows ON shows.id = shows_users.show_id
  WHERE shows.name = 'Shitfaced Shakespeare';
   count 
  -------
       7
  (1 row)
  ```

  20. Select all of the user names and the count of shows they're going to see.
  ```
  # SELECT users.name, COUNT(shows.name) FROM users
  INNER JOIN shows_users ON users.id = shows_users.user_id
  INNER JOIN shows ON shows.id = shows_users.show_id
  GROUP BY users.name;

         name       | count 
  ------------------+-------
   Chris Flint      |     4
   Euan Blackledge  |     4
   Joe Maher        |     5
   Ashleigh Adams   |     4
   Russell Williams |     6
   Nico di Lillo    |     4
   Megan Strachan   |     5
   Keith Douglas    |     6
   Sam Werngren     |     5
   Marie Moyles     |     7
   Iain Stewart     |     6
   Natalie Simpson  |     8
   Davide de Lerma  |     7
   Jay Chetty       |     5
   Rick Henri       |     5
  (15 rows)
  ```

  21. SELECT all users who are going to a show at 17:15.
  ```
  # SELECT users.name FROM users
  INNER JOIN shows_users ON users.id = shows_users.user_id
  INNER JOIN shows ON shows_users.show_id = shows.id
  INNER JOIN times ON shows.id = times.show_id
  WHERE time = '17:15';

         name       
  ------------------
   Rick Henri
   Keith Douglas
   Euan Blackledge
   Joe Maher
   Marie Moyles
   Sam Werngren
   Davide de Lerma
   Megan Strachan
   Russell Williams
   Sam Werngren
   Iain Stewart
   Natalie Simpson
  (12 rows)
  ```
## Extra Credit?

  Question 4 can be made more dynamic...
  ```
  # INSERT INTO users (name) VALUES ('Val Gibson');
  ```
  This adds Val back into the DB (We deleted her previously). Note that Val's `id` is now 20...
  ```
  # SELECT * FROM users WHERE name LIKE 'Val%';
   id |    name    
  ----+------------
   20 | Val Gibson
  (1 row)
  ```
  The queries to find Val and the show's `id`s can be entered straight into the `INSERT INTO shows_users` query...
  ```
  # INSERT INTO shows_users (show_id, user_id) VALUES (
    (SELECT id FROM shows WHERE name = 'Two girls, one cup of comedy'), 
    (SELECT id FROM users WHERE name = 'Val Gibson'));

INSERT 0 1
  ```
  And Val's correct `id` is paired with the show's `id`, which can be proven with a `JOIN`...
  ```
  # SELECT * FROM shows_users
  INNER JOIN users ON users.id = shows_users.user_id
  WHERE users.name LIKE 'Val%';
    id | show_id | user_id | id |    name
   ----+---------+---------+----+------------
    83 |      12 |      20 | 20 | Val Gibson
    ```

    BOOM!

