# SQL практика (QA)

## Описание

В этом репозитории собраны SQL-запросы, выполненные в рамках обучения тестированию.
Использовал SQL для работы с данными, проверки корректности информации и анализа связей между таблицами.

Основные задачи:

* выборка и фильтрация данных
* проверка бизнес-логики
* анализ связей между таблицами

База данных: `qa_shop`

---

## Схема БД

### users

```sql
user_id (smallint)
login (varchar)
password (varchar)
token (varchar)
```

### orders

```sql
order_id (smallint)
user_id (smallint)
status (varchar)
payment_date (datetime)
delivery_date (datetime)
total (decimal)
```

### products

```sql
product_id (smallint)
name (varchar)
description (text)
price (decimal)
category (varchar)
manufacturer (varchar)
imageUrl (varchar)
freeShipping (tinyint)
```


---

# SELECT запросы

**Вывести все продукты**

```sql
SELECT * FROM qa_shop.products;
```

**Продукты Apple в категории Phones**

```sql
SELECT * 
FROM qa_shop.products 
WHERE manufacturer = 'Apple' AND category = 'Phones';
```

**Найти продукты, где в названии есть 'sa'**

```sql
SELECT name, price 
FROM qa_shop.products 
WHERE name LIKE '%sa%';
```

**Продукты с ценой от 100 до 1000**

```sql
SELECT name, price 
FROM qa_shop.products 
WHERE price BETWEEN 100 AND 1000;
```

**Сумма товаров Samsung**

```sql
SELECT SUM(price) AS 'SAMSUNG TOTAL PRICE' 
FROM qa_shop.products 
WHERE manufacturer = 'Samsung';
```

**Сортировка по цене (убывание)**

```sql
SELECT name, price 
FROM qa_shop.products 
ORDER BY price DESC;
```

**Уникальные производители**

```sql
SELECT DISTINCT manufacturer 
FROM qa_shop.products;
```

**Первые 2 уникальные категории**

```sql
SELECT DISTINCT category 
FROM qa_shop.products 
LIMIT 2;
```

**Названия из 12 символов, начинаются с A**

```sql
SELECT name 
FROM qa_shop.products 
WHERE name LIKE 'A___________';
```

**Средняя цена продуктов**

```sql
SELECT AVG(price) AS 'PRODUCTS AVG PRICE' 
FROM qa_shop.products;
```

**Продукты Samsung и Huawei**

```sql
SELECT name, description 
FROM qa_shop.products 
WHERE manufacturer IN ('Samsung', 'Huawei');
```

---

**UNION**

```sql
SELECT name FROM qa_shop.products
UNION
SELECT CAST(order_id AS CHAR) FROM qa_shop.orders;
```

**GROUP BY + HAVING**

```sql
SELECT category, COUNT(*) AS product_count 
FROM qa_shop.products 
GROUP BY category 
HAVING COUNT(*) > 15;
```

**CASE**

```sql
SELECT 
  manufacturer,
  category,
  price,
  name,
  CASE manufacturer
    WHEN 'Apple' THEN 'Это продукт компании Apple'
    WHEN 'Samsung' THEN 'Это продукт компании Samsung'
    WHEN 'Huawei' THEN 'Это продукт компании Huawei'
    WHEN 'Xiaomi' THEN 'Это продукт компании Xiaomi'
  END AS 'Company Message'
FROM qa_shop.products;
```

---

# JOIN запросы

**Пользователь и его заказы**

```sql
SELECT u.login, o.order_id, o.total
FROM users u
JOIN orders o ON u.user_id = o.user_id
WHERE u.user_id = 6;
```

**Заказы, товары и количество**

```sql
SELECT oi.order_id, p.name, oi.quantity
FROM order_items oi
JOIN products p ON oi.product_id = p.product_id;
```

**Все пользователи и их заказы (включая тех, у кого нет заказов)**

```sql
SELECT u.login, o.order_id
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id;
```

**Оплаченные заказы и товары**

```sql
SELECT 
    oip.order_id, 
    p.name
FROM order_items_paid oip
RIGHT JOIN products p ON oip.product_id = p.product_id
ORDER BY oip.order_id;
```

**Подзапрос (товары дороже Samsung Active 5)**

```sql
SELECT name, price
FROM products
WHERE price > (
    SELECT price 
    FROM products 
    WHERE name = 'Samsung Active 5'
);
```

