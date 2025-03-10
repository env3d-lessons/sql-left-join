A **LEFT JOIN** in SQL retrieves all records from the **left table**
(the one before `JOIN`), and the matching records from the **right table**
(the one after `JOIN`). If there’s no match in the right table, it
returns `NULL` for those columns.

### Syntax:
```sql
SELECT *
FROM table1
LEFT JOIN table2 ON table1.id = table2.id;
```

### Example:

#### Tables:
**Users**
| id | name  |
|----|-------|
| 1  | Alice |
| 2  | Bob   |
| 3  | Carol |

**Orders**
| id | user_id | product    |
|----|---------|-----------|
| 1  | 1       | Laptop    |
| 2  | 1       | Mouse     |
| 3  | 2       | Keyboard  |

#### Query:
```sql
SELECT Users.id, Users.name, Orders.product
FROM Users
LEFT JOIN Orders ON Users.id = Orders.user_id;
```

#### Result:
| id | name  | product   |
|----|-------|----------|
| 1  | Alice | Laptop   |
| 1  | Alice | Mouse    |
| 2  | Bob   | Keyboard |
| 3  | Carol | NULL     |

Since Carol has no orders, `NULL` appears for the **Orders.product** column.

# Left-Join vs Correlated Subquery

A **correlated subquery** can sometimes replace a **LEFT JOIN**, but it depends
on the use case. Correlated subqueries can simplify certain queries, but they may
be **less efficient** than a `LEFT JOIN` because they execute once per row in
the outer query.

First prepare the sample dataset:

```sql
CREATE TABLE Users (
    id INT PRIMARY KEY,
    name VARCHAR(50)
);

CREATE TABLE Orders (
    id INT PRIMARY KEY,
    user_id INT,
    product VARCHAR(100),
    order_date DATE,
    FOREIGN KEY (user_id) REFERENCES Users(id)
);

INSERT INTO Users (id, name) VALUES
(1, 'Alice'),
(2, 'Bob'),
(3, 'Carol'),
(4, 'David');

INSERT INTO Orders (id, user_id, product, order_date) VALUES
(1, 1, 'Laptop', '2024-01-10'),
(2, 1, 'Mouse', '2024-01-15'),
(3, 2, 'Keyboard', '2024-01-20'),
(4, 2, 'Monitor', '2024-02-01'),
(5, 3, 'Tablet', '2024-02-05');

```

---

### **Example 1: Using LEFT JOIN**
#### Scenario: Get each user's most recent order.

```sql
SELECT Users.id, Users.name, Orders.product, Orders.order_date
FROM Users
LEFT JOIN Orders ON Users.id = Orders.user_id
AND Orders.order_date = (
    SELECT MAX(order_date) FROM Orders WHERE Orders.user_id = Users.id
);
```

🔹 **Pros**: More efficient in many cases (indexing helps).  
🔹 **Cons**: Slightly more complex than a subquery.

---

### **Example 2: Using a Correlated Subquery**
```sql
SELECT 
    id, 
    name, 
    (SELECT product FROM Orders o
     WHERE o.user_id = Users.id 
     ORDER BY order_date DESC 
     LIMIT 1) AS latest_product,
    (SELECT order_date FROM Orders o
     WHERE o.user_id = Users.id 
     ORDER BY order_date DESC 
     LIMIT 1) AS latest_order_date
FROM Users;
```

🔹 **Pros**: More readable for simple lookups.  
🔹 **Cons**: Runs a subquery **for each row**, which can be slow on large datasets.

# Exercises

The chinook database will be used for the following exercises.

The Chinook data model represents a digital media store, including tables for artists,
albums, media tracks, invoices and customers.

<img width="836" alt="image" src="https://github.com/lerocha/chinook-database/assets/135025/cea7a05a-5c36-40cd-84c7-488307a123f4">

## 1.sql

Retrieves all artists without an Album.  Show just the Artist's name sort alphabetically.

Partial Output:

```
A Cor Do Som
Academy of St. Martin in the Fields, Sir Neville Marriner & William Bennett
Aerosmith & Sierra Leone's Refugee Allstars
Avril Lavigne
Azymuth
Baby Consuelo
Banda Black Rio
Barão Vermelho
Bebel Gilberto
Ben Harper
Big & Rich
Black Eyed Peas
Charlie Brown Jr.
Christina Aguilera featuring BigElf
```

## 2.sql

Find which Genre, and no associated Track.  Show only the Genre name.

## 3.sql

Which playlist has no songs?  Shows the playlist id and playlist name.

Expected Output:

```
2|Movies
4|Audiobooks
6|Audiobooks
7|Movies
9|Music Videos
```

## 4.sql

Output Track name that has never been ordered

Partial Output:

```
"40"
"Eine Kleine Nachtmusik" Serenade In G, K. 525: I. Allegro
#1 Zero
(Da Le) Yaleo
(I Can't Help) Falling In Love With You
(Oh) Pretty Woman
(There Is) No Greater Love (Teo Licks)
(We Are) The Road Crew
...In Translation
.07%
02 - Sanctuary
03 - Remember Tomorrow
06 - Transylvania
07 - Strange World
08 - Charlotte the Harlot

```

## 5.sql

Which employees have no one to report to, how old are these employees?
Show the name and the age of the employee.

NOTE: The answer needs to be in the form "[First Name] [Last Name] is [XX] years old"

## 6.sql

For each employee, show the First Name, Last Name, and Total amount of
sales they have done.  Show 0 if the employee has not made any sales.
Order by the total sales in descending order.

```
Peacock|Jane|833.040000000001
Park|Margaret|775.400000000001
Johnson|Steve|720.160000000001
Adams|Andrew|0
Edwards|Nancy|0
Mitchell|Michael|0
King|Robert|0
Callahan|Laura|0
```

## 7.sql

For each customer, show their First Name, Last Name, and their favorite genre.
Sort by first name and then last name.

To find out their favorite genre, count the genre of all the tracks
the customer have purchased and pick the highest one.

Partial Output:

```
Aaron|Mitchell|Latin
Alexandre|Rocha|Latin
Astrid|Gruber|Rock
Bjørn|Hansen|Rock
Camille|Bernard|Rock
Daan|Peeters|Rock
Dan|Miller|Latin
Diego|Gutiérrez|Alternative & Punk
Dominique|Lefebvre|Rock
Eduardo|Martins|Rock
Edward|Francis|Rock
Ellie|Sullivan|Rock
Emma|Jones|Latin
Enrique|Muñoz|Rock
Fernanda|Ramos|Rock
```

## 8.sql

Somemtimes, database have inconsistent data.  Find all playlist id and names that contains
non-existant tracks sort by playlistid ascending.

Expected Output:

```
1|Music
5|90’s Music
8|Music
12|Classical
14|Classical 101 - Next Steps
```

## 9.sql

Which song is NOT on playlist.  Display only song name.

# Hand-in

Test your solution by executing the following command on the bash terminal:

```shell
$ pytest
```

When you are satisified, execute the following commands to submit:

```shell
$ git add -A
$ git commit -m 'submit'
$ git push
```
