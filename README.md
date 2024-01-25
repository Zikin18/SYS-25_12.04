# Домашнее задание к занятию «SQL. Часть 2» - Юрий Белобородов


### Задание 1

Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию: 
- фамилия и имя сотрудника из этого магазина;
- город нахождения магазина;
- количество пользователей, закреплённых в этом магазине.

Ответ:

```
SELECT 
	store.store_id, 
	staff.first_name, 
	staff.last_name, 
	city.city, 
	store.last_update,
	COUNT(c.customer_id) as customerCount
FROM sakila.store
JOIN staff ON staff.staff_id = store.manager_staff_id
JOIN address ON address.address_id = store.address_id 
JOIN city ON city.city_id = address.city_id
JOIN customer c ON c.store_id = store.store_id 
GROUP BY store.store_id
HAVING COUNT(c.customer_id) > 300
```

![1-1.png](https://github.com/Zikin18/SYS-25_12.04/blob/master/1-1.png)


### Задание 2

Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

Ответ:

```
SELECT 
	COUNT(f.film_id) as count
FROM sakila.film f
WHERE f.`length` > (SELECT AVG(f.`length`) FROM sakila.film f)
```

![2-1.png](https://github.com/Zikin18/SYS-25_12.04/blob/master/2-1.png)


### Задание 3

Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.

Ответ:

```
SELECT
    DATE_FORMAT(p.payment_date, '%m.%Y') AS month,
    SUM(p.amount) AS sum,
    COUNT(r.rental_id) AS rentalCount
FROM payment p
JOIN rental r ON p.rental_id = r.rental_id
GROUP BY month
ORDER BY sum DESC
LIMIT 1;
```

![3-1.png](https://github.com/Zikin18/SYS-25_12.04/blob/master/3-1.png)


## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

### Задание 4*

Посчитайте количество продаж, выполненных каждым продавцом. Добавьте вычисляемую колонку «Премия». Если количество продаж превышает 8000, то значение в колонке будет «Да», иначе должно быть значение «Нет».


Ответ:

```
SELECT 
	s.first_name,
	s.last_name,
	COUNT(p.payment_id) AS paymentsCount,
	CASE
		WHEN COUNT(p.payment_id) > 8000 THEN 'Да'
		ELSE 'Нет'
	END AS BONUS
FROM staff s 
JOIN payment p ON p.staff_id = s.staff_id
GROUP BY s.staff_id 
```

![4-1.png](https://github.com/Zikin18/SYS-25_12.04/blob/master/4-1.png)

### Задание 5*

Найдите фильмы, которые ни разу не брали в аренду.

Ответ:

```
SELECT f.title 
FROM film f 
LEFT JOIN inventory i ON i.film_id = f.film_id 
LEFT JOIN rental r ON r.inventory_id = i.inventory_id 
GROUP BY f.film_id 
HAVING COUNT(r.rental_id) = 0
```

![5-1.png](https://github.com/Zikin18/SYS-25_12.04/blob/master/5-1.png)
