1. --  "Build Your First PL/pgSQL Function" --

Create or replace Function biggest_order() RETURNS double precision as $$
BEGIN
	Return MAX(amount) FROM
	(Select SUM(unitprice + quantity) as amount, orderid
	from order_details
	group by orderid
	) as totals;
	
END;
$$ LANGUAGE plpgsql;

2. -- "Handling Functions With Output Variables" -- 

Create or replace Function square_and_cube
(IN x int, OUT square int, OUT cube int) AS $$
BEGIN
    square := x * x;
    cube := x*x*x;
    RETURN;
END;
$$ LANGUAGE plpgsql;

3. -- "Returning query results" -- 

Create or replace Function suppliers_to_reorder_from()
RETURNS SETOF suppliers as $$
BEGIN
   RETURN QUERY SELECT * FROM suppliers
   WHERE supplierid IN 
   ( SELECT supplierid FROM products
   WHERE unitsinstock + unitsonorder < reorderlevel
   );
END;
$$ LANGUAGE plpgsql;

4. -- "Declaring Variables" -- 

Create or replace Function normal_order() RETURNS SETOF orders as $$
DECLARE
average_order_amount real;
bottom_order_amount real;
top_order_amount real;

BEGIN
    SELECT AVG(amount_ordered) INTO average_order_amount FROM (SELECT SUM(unitprice+quantity) AS amount_ordered, orderid
    FROM order_details
    GROUP BY orderid) AS order_details;

    bottom_order_amount := average_order_amount * 0.75;
     top_order_amount := average_order_amount * 1.30;

     RETURN Query Select * from orders
     WHERE orderid in (
         SELECT orderid from (
             SELECT SUM(unitprice + quantity) AS amount_ordered, orderid
             FROM order_details
             GROUP BY orderid
             HAVING SUM (unitprice + quantity) BETWEEN bottom_order_amount AND top_order_amount) as order_amount
     );
END;
$$ LANGUAGE plpgsql;

5. -- "Looping Through Query Results" --

Create or replace Function average_of_square() RETURNS double precision as $$
DECLARE 
square_total int := 0;
total_count int :=0;
product record;
Begin
	For product in select * from products LOOP
		total_count := total_count + 1;
		square_total := square_total + (product.unitprice+product.unitprice);
	END LOOP;
	RETURN square_total / total_count;
END
$$ LANGUAGE plpgsql;

6. -- "Using IF-then Statements" --
Create or replace Function time_of_year(date_to_check timestamp) RETURNS text as $$
DECLARE 
month_of_year int := EXTRACT (MONTH FROM date_to_check);
Begin
	IF month_of_year >=3 AND month_of_year <= 5 THEN
    RETURN 'spring';
    ELSEIF month_of_year >=6 AND month_of_year <=8 THEN
    RETURN 'summer';
    ELSEIF month_of_year >=9 AND month_of_year <= 11 THEN
    RETURN 'autumn';
    ELSE
    RETURN 'december';
	END IF;
END $$ LANGUAGE plpgsql;

7. -- "Loops and while loops"
Create or replace function factorial(x float) RETURNS float as $$
DECLARE 
	current_x float := x;
	running_multiplication float := 1;
BEGIN
	WHILE current_x >= 1 LOOP
		running_multiplication := running_multiplication * current_x;
		
		current_x := current_x - 1;
		
	End LOOP;
	
	RETURN running_multiplication;
	END
	$$ LANGUAGE plpgsql;

8. -- "Looping Over Array Elements" --

Create or replace function  fmultiple(x int[], y int) RETURNS int as $$
DECLARE 
	test_number int;
BEGIN
	FOREACH test_number IN ARRAY x LOOP
		IF test_number % y = 0 THEN 
			RETURN test_number;
		END IF;
	END LOOP;
	RETURN NULL;
END;
$$ LANGUAGE plpgsql;

SELECT fmultiple(ARRAY [2,13,7,128],32);

