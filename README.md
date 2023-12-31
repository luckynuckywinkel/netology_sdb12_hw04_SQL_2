# Домашнее задание к занятию «SQL. Часть 2», Лебедев А. И., FOPS-10


### Задание 1

Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию: 
- фамилия и имя сотрудника из этого магазина;
- город нахождения магазина;
- количество пользователей, закреплённых в этом магазине.

### Решение:  

- Все SQL-запросы будут производиться в загруженном дампе базы **sakila** из предыдущего задания. Как и что я создавал, можете посмотреть  <code>[тут](https://github.com/luckynuckywinkel/netology_sdb12_hw03_SQL_1 "sdb12_hw03_SQL_1")</code>.

- Итак, составим запрос, удовлетворяющий условиям нашей задачи:

```
mysql> SELECT
    ->     CONCAT(staff.first_name, ' ', staff.last_name) AS manager_name,
    ->     city.city AS store_city,
    ->     COUNT(customer.customer_id) AS how_many_customers
    -> FROM
    ->     store
    ->     JOIN staff ON store.manager_staff_id = staff.staff_id
    ->     JOIN address ON store.address_id = address.address_id
    ->     JOIN city ON address.city_id = city.city_id
    ->     JOIN customer ON store.store_id = customer.store_id
    -> GROUP BY
    ->     store.store_id
    -> HAVING
    ->     COUNT(customer.customer_id) > 300;
+---------------+------------+----------------+
| manager_name | store_city | how_many_customers |
+---------------+------------+----------------+
| Mike Hillyer  | Lethbridge |            326 |
+---------------+------------+----------------+
1 row in set (0.02 sec)
```

- Здесь мы делаем следующее:

Объединяем таблицы, чтобы получить данные о магазине, его менеджере, городе и клиентах.  

Затем мы группируем данные по store_id (идентификатору магазина), чтобы агрегатные функции применялись к каждому магазину отдельно.  

Используем агрегатную функцию COUNT для подсчета количества клиентов в каждом магазине.  

Затем применяем условие HAVING для фильтрации магазинов, оставляя только те, где количество клиентов больше 300.

---

### Задание 2

Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.  

### Решение:  

- Это относительно не сложный запрос, составим его:

```
mysql> SELECT COUNT(*) AS film_count
    -> FROM film
    -> WHERE length > (SELECT AVG(length) FROM film);
+------------+
| film_count |
+------------+
|        489 |
+------------+
1 row in set (0.01 sec)
```

- Здесь мы используем функцию AVG, которая вычисляет среднее значение числового столбца в наборе данных. В нашем случае, это **length**

---

### Задание 3

Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.  

### Решение:  

- Составим запрос:

```
mysql> SELECT
    ->     DATE_FORMAT(payment.payment_date, '%Y-%m') AS current_month,
    ->     COUNT(rental.rental_id) AS rental_count
    -> FROM
    ->     payment
    ->     JOIN rental ON payment.rental_id = rental.rental_id
    -> GROUP BY
    ->     current_month
    -> ORDER BY
    ->     SUM(payment.amount) DESC
    -> LIMIT 3;
+---------------+--------------+
| current_month | rental_count |
+---------------+--------------+
| 2005-07       |         6709 |
| 2005-08       |         5686 |
| 2005-06       |         2311 |
+---------------+--------------+
3 rows in set (0.12 sec)
```

- Я взял LIMIT 3, группируем по алиасу curent_month и выводим сумму платежей (payment.amount) по убыванию


---

