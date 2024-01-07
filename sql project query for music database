-- WHO IS THE SENIOR MOST EMPLOYEE?
SELECT * FROM employee
ORDER BY levels DESC
LIMIT 1;

--WHICH COUNTRY HAVE THE MOST INVOICE?
SELECT COUNT(*) AS HIGHEST_INVOICE_COUNT,billing_country
FROM invoice
GROUP BY billing_country
ORDER BY HIGHEST_INVOICE_COUNT DESC;

--WHAT ARE TOP 3 VALUES OF TOTAL INVOICE
SELECT total FROM invoice
ORDER BY total DESC
LIMIT 3;

-- WHICH CITY HAS THE BEST CUSTOMERS? WE WOULD LIKE TO THROW A PROMOTIONAL MUSIC FESTIVAL CITY WE MADE THE MOST MONEY. WRITE A QUERY THAT RETURNS ONE CITY TAHT HAS THE HIGHEST SUM OF INVOICE TOTALS. RETURN BOTH THE CITY NAME & SUM OF ALL INVOICE TOTAL
SELECT SUM (total) AS TOTAL_SALE ,billing_city FROM INVOICE
GROUP BY billing_city
ORDER BY TOTAL_SALE DESC;

-- WHO IS THE BEST CUSTOMER? THE CUSTOMER WHO HAS SPENT THE MOST MONEY WILL BE DECLARED THE BEST CUSTOMER. WRITE A QUERY THAT RETURN THE PERSON WHO HAS SPENT THE MOST MONEY.
SELECT customer.customer_id,customer.first_name,customer.last_name,customer.country, SUM(invoice.total) AS TOTAL 
FROM customer
JOIN invoice ON customer.customer_id = invoice.customer_id
GROUP BY customer.customer_id,customer.first_name,customer.last_name,customer.country
ORDER BY TOTAL DESC
LIMIT 1

--WRITE QUERY TO RETURN THE EMAIL, FIRST NAME, LAST NAME & GENRE OF ALL ROCK MUSIC LISTENERS. RETURN YOUR LIST ORDERED ALPHABETICALLY BY EMAIL STARTING WITH A
SELECT customer.first_name,customer.last_name,customer.email AS EMAIL, genre.name AS GENRE
FROM customer
JOIN invoice ON customer.customer_id = invoice.customer_id
JOIN invoice_line ON invoice.invoice_id = invoice_line.invoice_id
JOIN track ON invoice_line.track_id = track.track_id
JOIN genre ON track.genre_id = genre.genre_id
WHERE genre.name = 'Rock'
GROUP BY customer.first_name,customer.last_name,customer.email, genre.name
ORDER BY EMAIL 

-- LETS INVITE THE ARTIST WHO HAVE WRITTEN THE MOST ROCK MUSIC IN OUT DATASET. WRITE A QUERY THAT RETURNS THE ARTIST NAME & TOTAL TRACK COUNT OF THE TOP 10 ROCK BAND
SELECT artist.artist_id, artist.name, genre.name AS genre, COUNT (artist.artist_id) AS NUMBER_OF_SONGS
FROM artist
JOIN album ON artist.artist_id = album.artist_id
JOIN track ON album.album_id = track.album_id
JOIN genre ON track.genre_id = genre.genre_id
WHERE genre.name = 'Rock'
GROUP BY artist.artist_id, artist.name, genre
ORDER BY NUMBER_OF_SONGS DESC
LIMIT 10
 
--RETURN ALL THE TRACK NAMES THAT HAVE A SONG LENGTH LONGER THAN THE AVERAGE SONG LENGTH. RETURN THE NAME & MILLISECONDS FOR EACH TRACK. ORDER BY THE SONG LENGTH WITH LONGEST SONGS LISTED FIRST.
SELECT name,milliseconds
FROM track
WHERE track.milliseconds > 
(SELECT AVG(track.milliseconds) AS AVG_TRACK_LENGTH FROM track)
ORDER BY milliseconds DESC

--FIND HOW MUCH AMOUNT SPENT BY EACH CUSTOMER ON ARTIST? WRITE A QUERY TO RETURN CUSTOMER NAME, ARTIST NAME & total spent 
WITH BEST_SELLING_ARTIST AS(
  SELECT artist.artist_id AS ARTIST_ID,artist.name AS ARTIST_NAME,
  SUM (invoice_line.unit_price*invoice_line.quantity) AS TOTAL_SALES
  FROM invoice_line
  JOIN track ON invoice_line.track_id =track.track_id
  JOIN album ON track.album_id = album.album_id
  JOIN artist ON album.artist_id =artist.artist_id
  GROUP BY 1,2
  ORDER BY 3 DESC
  LIMIT 1
)
SELECT customer.customer_id,customer.first_name,customer.last_name,BSA.artist_name,
sum (invoice_line.unit_price*invoice_line.quantity) as amount_spent
from invoice 
JOIN customer on invoice.customer_id =customer.customer_id
JOIN invoice_line on invoice.invoice_id =invoice_line.invoice_id
join track on invoice_line.track_id = track.track_id
JOIN album on track.album_id = album.album_id
JOIN BEST_SELLING_ARTIST BSA ON BSA.artist_id = album.artist_id
group by 1,2,3,4
order by 5 desc;

--WE WANT TO FIND OUT THE MOST POPULAR MUSIC GENRE FOR EACH COUNTRY. WE DETERMINE THE MOST POPULAR GENRE AS THE GENRE WITH THE HIGHEST AMOUNT OF PURCHASES. WRITE QUERY THAT RETURNS EACH COUNTRY ALONG WITH THE TOP GENRE. FOR COUNTRIES WHERE THE MAXIMUM NUMBER OF PURCHASES IS SHARED RETURN ALL GENRES 
WITH POPULAR_GENRE AS (
  SELECT COUNT(invoice_line.quantity) AS PURCHASES,customer.country,genre.name,genre.genre_id,
  ROW_NUMBER() OVER(PARTITION BY customer.country ORDER BY COUNT(invoice_line.quantity) DESC) AS ROW_NO
  FROM  customer
  JOIN invoice ON customer.customer_id =invoice.customer_id
  JOIN invoice_line ON invoice.invoice_id = invoice_line.invoice_id
  JOIN track ON invoice_line.track_id = track.track_id
  JOIN genre ON track.genre_id = genre.genre_id
  GROUP BY 2,3,4
  ORDER BY 2 ASC, 1 DESC
  )
  SELECT * FROM POPULAR_GENRE WHERE ROW_NO <= 1

--WRITE A QUERY THAT DETERMINES THE CUSTOMER THAT HAS SPENT THE MOST ON MUSIC FOR EACH COUNTRY. WRITE A QUERY THAT RETURNS THE COUNTRY ALONG WITH THE TOP CUSTOMER & HOW MUCH THEY SPENT. FOR COUNTRIES WHERE THE AMOUNT SPENT IS SHARED, PROVIDE ALL CUSTOMERS WHO SPENT THIS AMOUNT
WITH CUSTOMER_WITH_COUNTRY AS(
SELECT customer.customer_id,customer.first_name,customer.last_name,billing_country, SUM(invoice.total) AS TOTAL_PURCHASE,
ROW_NUMBER() OVER(PARTITION BY billing_country ORDER BY SUM(invoice.total) DESC) AS ROW_NO
FROM customer
JOIN invoice ON customer.customer_id = invoice.customer_id
GROUP BY 1,2,3,4
ORDER BY 4,5 DESC
)
SELECT * FROM CUSTOMER_WITH_COUNTRY WHERE ROW_NO <= 1

--- RECURSIVE METHOD
WITH RECURSIVE CUSTOMER_WITH_COUNTRY AS(
SELECT customer.customer_id,customer.first_name,customer.last_name,billing_country, SUM(invoice.total) AS TOTAL_PURCHASE,
ROW_NUMBER() OVER(PARTITION BY billing_country ORDER BY SUM(invoice.total) DESC) AS ROW_NO
FROM customer
JOIN invoice ON customer.customer_id = invoice.customer_id
GROUP BY 1,2,3,4
ORDER BY 4,5 DESC
),
COUNTRY_MAX_SPENDING AS(
  SELECT billing_country,MAX(TOTAL_PURCHASE) AS MAX_SPENDING
  FROM CUSTOMER_WITH_COUNTRY
  GROUP BY billing_country)
  
  SELECT CC.BILLING_COUNTRY,CC.TOTAL_PURCHASE,CC.FIRST_NAME,CC.LAST_NAME,CC.CUSTOMER_ID
  FROM CUSTOMER_WITH_COUNTRY CC
  JOIN COUNTRY_MAX_SPENDING MS ON CC.BILLING_COUNTRY = MS.BILLING_COUNTRY
  WHERE CC.TOTAL_PURCHASE = MS.MAX_SPENDING
  ORDER BY 1;
  
  
