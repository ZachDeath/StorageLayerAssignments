-- Question 1: Creating the tables --

create table authors (
	id SERIAL,
	name VARCHAR(50)
    PRIMARY KEY (id),
)

create table books (
	id SERIAL,
	title VARCHAR(100),
	author_id int,
    PRIMARY KEY (id),
	FOREIGN KEY (author_id) REFERENCES authors(id)
)

create table reviews (
	id SERIAL,
	rating int,
	reviewer_id INTEGER,
	book_id INT,
	PRIMARY KEY (id),
	FOREIGN KEY (reviewer_id) REFERENCES authors(id),
	FOREIGN KEY (book_id) REFERENCES books(id)
)

-- Question 1: Answers --
1) SELECT books.title, authors.name
FROM books
INNER JOIN authors
ON books.author_id = authors.id;

2) SELECT books.title, authors.name
FROM authors
left JOIN books
ON books.author_id = authors.id; 

OR

SELECT books.title, authors.name
FROM books
RIGHT JOIN authors
ON authors.id = books.author_id

3) select books.title, authors.name, reviews.rating, authors.id, reviews.reviewer_id
from books
join authors
on books.author_id = authors.id
join reviews
on books.id = reviews.book_id
where reviews.reviewer_id = authors.id

4) select authors.id, count(books.title)
from books
join authors
on authors.id = books.author_id
GROUP BY  authors.id

OR WITH ONE TABLE

select books.author_id, count(books.title) from books
GROUP BY books.author_id

5) 
select authors.name, count(books.title)
from books
join authors
on authors.id = books.author_id
GROUP BY  authors.id

-- Question 2: Answers --
1) select manufacturer, sum(units_sold * price) total_revenue from phones
group by manufacturer having sum(units_sold * price) > 200000;

2) SELECT * FROM phones
order by price DESC
offset 1 rows
LIMIT 2;

3) SELECT manufacturer, price
FROM phones
WHERE price < 170


3b) SELECT
	manufacturer,
	COUNT(*) total_phones
FROM
	phones
GROUP BY
	manufacturer
HAVING 
	COUNT(*) >= 2

4) SELECT name, price, (select max(price) FROM phones) / (price * 1.0)  price_ratio FROM phones
GROUP BY name, price

5)  SELECT name, price
FROM phones
WHERE units_sold > 5000

6) SELECT name, manufacturer
FROM phones
WHERE manufacturer = 'Apple' OR manufacturer = 'Samsung'

7) SELECT name , sum(units_sold * price) total_revenue FROM phones
GROUP BY name HAVING sum(units_sold * price) > 100000;

	-- Question 3: Answers --
Q1) SELECT (select count(paid) FROM orders WHERE paid = true) paid, (SELECT count(paid) FROM orders WHERE paid = false) unpaid from orders
group by 1

Q2) SELECT first_name, last_name, paid FROM users
JOIN orders on users.id = orders.user_id