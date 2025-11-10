# Лабораторна робота 2. Створення складних SQL запитів

## Загальна інформація

**Здобувач освіти:** Федорчук Богдан
**Група:** ІПЗ-31
**Обраний рівень складності:** 1

## Виконання завдань

### Рівень 1

#### 1. З'єднання таблиць

**Завдання 1.1:** INNER JOIN - список товарів з категоріями та постачальниками

```sql
SELECT p.product_name, c.category_name, s.company_name, p.unit_price
FROM products p
INNER JOIN categories c ON p.category_id = c.category_id
INNER JOIN suppliers s ON p.supplier_id = s.supplier_id
ORDER BY c.category_name, p.product_name;

```

**Результат виконання:**


![Скріншот](300.png)


**Пояснення:** В запиті з'єднані таблиці products, categories та suppliers. Запит виводить список товарів разом із назвами їхніх категорій і постачальників, а також ціною кожного товару.



**Завдання 1.2:** LEFT JOIN - клієнти з кількістю замовлень

```sql
SELECT c.contact_name, c.customer_type, r.region_name,
      COUNT(o.order_id) as order_count
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
LEFT JOIN regions r ON c.region_id = r.region_id
GROUP BY c.customer_id, c.contact_name, c.customer_type, r.region_name
ORDER BY order_count DESC;


```

**Результат виконання:**


![Скріншот](308.png)


**Пояснення:** INNER JOIN вибирає лише ті рядки, у яких є збіг між обома таблицями. А LEFT JOIN, навпаки, вибирає всі рядки з лівої таблиці у цьому випадку customers, навіть якщо у правій таблиці orders немає відповідних записів.



**Завдання 1.3:** Множинне з'єднання - детальна інформація про замовлення

```sql
SELECT 
    o.order_id,
    COALESCE(c.company_name, c.contact_name) AS customer  ,
    e.full_name, p.product_name, s.company_name, od.quantity, od.unit_price,
    (od.quantity * od.unit_price) AS total_price,
     o.order_date, o.freight 
FROM orders o
INNER JOIN customers c ON o.customer_id = c.customer_id
INNER JOIN employee_performance e ON o.employee_id = e.employee_id
INNER JOIN order_items od ON o.order_id = od.order_id
INNER JOIN products p ON od.product_id = p.product_id
INNER JOIN suppliers s ON p.supplier_id = s.supplier_id
ORDER BY o.order_id;

```

**Результат виконання:**


![Скріншот](301.png)


**Аналіз складності:** Цей запит містить 6 таблиць, пов’язаних через INNER JOIN. Тобто база даних має виконати послідовне об’єднання великої кількості даних, при цьому кожне з’єднання додає свій рівень обчислювальної складності.
Тут задіяні таблиці orders, customers, employee_performance, order_items, products, suppliers та кількість з’єднань 5. 
Послідовність з’єднань. 
Спочатку FROM orders o бере всі замовлення. Далі INNER JOIN customers c ON o.customer_id = c.customer_id додає інформацію про клієнта до кожного замовлення. INNER JOIN employee_performance e ON o.employee_id = e.employee_id додає працівника, який оформив це замовлення. INNER JOIN order_items od ON o.order_id = od.order_id додає рядки товарів, які входять до кожного замовлення. INNER JOIN products p ON od.product_id = p.product_id додає інформацію про сам товар. INNER JOIN suppliers s ON p.supplier_id = s.supplier_id додає постачальника кожного товару.


#### 2. Агрегатні функції

**Завдання 2.1:** Статистика товарів за категоріями

```sql
SELECT c.category_name,
      COUNT(p.product_id) as product_count,
      AVG(p.unit_price) as avg_price,
      MIN(p.unit_price) as min_price,
      MAX(p.unit_price) as max_price
FROM categories c
LEFT JOIN products p ON c.category_id = p.category_id
GROUP BY c.category_id, c.category_name
ORDER BY product_count DESC;

```

**Результат виконання:**


![Скріншот](302.png)



Цей запит виводить зведену статистику по категоріях товарів: скільки товарів у кожній категорії, середню ціну, мінімальну ціну, максимальну ціну. Це є агрегування даних із групуванням.

**Завдання 2.2:** Продажі за регіонами з використанням HAVING

```sql
SELECT 
    r.region_name,
    ROUND(SUM(od.quantity * od.unit_price), 2) AS total_sales
FROM orders o
INNER JOIN customers c ON o.customer_id = c.customer_id
INNER JOIN regions r ON c.region_id = r.region_id
INNER JOIN order_items od ON o.order_id = od.order_id
GROUP BY r.region_name
HAVING SUM(od.quantity * od.unit_price) > 0
ORDER BY total_sales DESC;

```

**Результат виконання:**


![Скріншот](303.png)


Цей запит об’єднує таблиці замовлень, клієнтів, регіонів і товарів у замовленнях, а потім підраховує загальну суму продажів для кожного регіону. З використанням SUM підсумовує суми по всьому регіону, GROUP BY групує всі дані за назвою регіону, HAVING залишає лише ті регіони, у яких сума продажів більше 0.

**Завдання 2.3:** Постачальники з кількістю товарів більше 2

```sql
SELECT 
    s.company_name,
    COUNT(p.product_id) AS quantity_products
FROM suppliers s
INNER JOIN products p ON s.supplier_id = p.supplier_id
GROUP BY s.company_name
HAVING COUNT(p.product_id) > 2
ORDER BY quantity_products DESC;

```

**Результат виконання:**


![Скріншот](304.png)


Цей запит виводить назву всіх постачальників кількість товарів у яких більше ніж 2. Тут використовується HAVING, який залишає тількі тих постачадьників, в який я більше ніж 2 товари.

#### 3. Базові підзапити

**Завдання 3.1:** Товари з ціною вище середньої по категорії

```sql
SELECT p.product_name, p.unit_price, c.category_name
FROM products p
INNER JOIN categories c ON p.category_id = c.category_id
WHERE p.unit_price > (
    SELECT AVG(p2.unit_price)
    FROM products p2
    WHERE p2.category_id = p.category_id
)
ORDER BY c.category_name, p.unit_price DESC;

```

**Результат виконання:**


![Скріншот](305.png)


Цей запит шукає товари які коштують більше ніж середнє ціна товарів в їхній ктегорії.


**Завдання 3.2:** Клієнти з замовленнями у 2024 році

```sql
SELECT 
    COALESCE(c.company_name, c.contact_name) AS customer,
    c.city, c.email, o.order_date
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
WHERE c.customer_id IN (
    SELECT o2.customer_id
    FROM orders o2
    WHERE EXTRACT(YEAR FROM o2.order_date) = 2024
)
AND EXTRACT(YEAR FROM o.order_date) = 2024
ORDER BY o.order_date;

```

**Результат виконання:**


![Скріншот](306.png)


Цей запис шукає всі замовлення які були здійснені в 2024 році. Тут використовується IN для виведення тількі тих замовлення які були здійсненні в 2024 році та AND для того щоб залишились твлькі дати замленьв 2024 році в колонці order_data.

**Завдання 3.3:** Товари з загальною кількістю продажів

```sql
SELECT 
    p.product_id,
    p.product_name,
    p.unit_price,
    (
        SELECT SUM(oi.quantity)
        FROM order_items oi
        WHERE oi.product_id = p.product_id
    ) AS total_sales
FROM products p
ORDER BY total_sales DESC NULLS LAST;
```

**Результат виконання:**


![Скріншот](307.png)


Цей запит виводить загальну кількість продажів товару, від товарів які продались найбільше до товарів які продались найменше. Тут використовується SELECT для для вирахування загальної суми продажів товару.

## Висновки: Я навчився створювати складні запити в SQL для аналізу даних, також навчився з'єднувати таблиці в SQl запитах. Опанував навичкі використання агрегатних функцій, групування даних та написання підзапитів для розв'язання аналітичних завдань у реляційних базах даних.

**Самооцінка**: 3

**Обгрунтування**: Тому що я зробив тількі 1 рівень складності.
