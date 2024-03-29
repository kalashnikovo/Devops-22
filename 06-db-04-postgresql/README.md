# Домашнее задание к занятию 4. «PostgreSQL»

## Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.
```bash
version: '3.5'
services:
  postgres:
    image: postgres:13
    environment:
      - POSTGRES_PASSWORD=postgres
      - POSTGRES_USER=postgres
    volumes:
      - ./data:/var/lib/postgresql/data
      - ./backup:/data/backup/postgres
    ports:
      - "5432:5432"
    restart: always
```
Подключитесь к БД PostgreSQL используя `psql`.

Воспользуйтесь командой `\?` для вывода подсказки по имеющимся в `psql` управляющим командам.

**Найдите и приведите** управляющие команды для:
- вывода списка БД
```bash
postgres=# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
(3 rows)
```
- подключения к БД
```bash
postgres=# \c postgres
You are now connected to database "postgres" as user "postgres".

```
- вывода списка таблиц
```bash
postgres=# \dt
Did not find any relations.
postgres=# \dt+
Did not find any relations.
```
- вывода описания содержимого таблиц
```bash
postgres=# \d pg_database
               Table "pg_catalog.pg_database"
    Column     |   Type    | Collation | Nullable | Default 
---------------+-----------+-----------+----------+---------
 oid           | oid       |           | not null | 
 datname       | name      |           | not null | 
 datdba        | oid       |           | not null | 
 encoding      | integer   |           | not null | 
 datcollate    | name      |           | not null | 
 datctype      | name      |           | not null | 
 datistemplate | boolean   |           | not null | 
 datallowconn  | boolean   |           | not null | 
 datconnlimit  | integer   |           | not null | 
 datlastsysoid | oid       |           | not null | 
 datfrozenxid  | xid       |           | not null | 
 datminmxid    | xid       |           | not null | 
 dattablespace | oid       |           | not null | 
 datacl        | aclitem[] |           |          | 
Indexes:
    "pg_database_datname_index" UNIQUE, btree (datname), tablespace "pg_global"
    "pg_database_oid_index" UNIQUE, btree (oid), tablespace "pg_global"
Tablespace: "pg_global"

```
- выхода из psql
```bash
postgres=# \q
```
## Задача 2

Используя `psql` создайте БД `test_database`.
```bash
postgres=# CREATE DATABASE test_database;
CREATE DATABASE
```
Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/master/06-db-04-postgresql/test_data).

Восстановите бэкап БД в `test_database`.
```bash
root@023ece8e96f1:/# psql -U postgres test_database < /data/backup/postgres/test_dump.sql
SET
SET
SET
SET
SET
 set_config 
------------
 
(1 row)

SET
SET
SET
SET
SET
SET
CREATE TABLE
ALTER TABLE
CREATE SEQUENCE
ALTER TABLE
ALTER SEQUENCE
ALTER TABLE
COPY 8
 setval 
--------
      8
(1 row)

ALTER TABLE
```
Перейдите в управляющую консоль `psql` внутри контейнера.
```bash
postgres=# \l
                                   List of databases
     Name      |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
---------------+----------+----------+------------+------------+-----------------------
 postgres      | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0     | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
               |          |          |            |            | postgres=CTc/postgres
 template1     | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
               |          |          |            |            | postgres=CTc/postgres
 test_database | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
(4 rows)

```
Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.
```bash
postgres=# \c test_database
You are now connected to database "test_database" as user "postgres".
test_database=# \d
              List of relations
 Schema |     Name      |   Type   |  Owner   
--------+---------------+----------+----------
 public | orders        | table    | postgres
 public | orders_id_seq | sequence | postgres
(2 rows)
test_database=# analyze verbose public.orders;
INFO:  analyzing "public.orders"
INFO:  "orders": scanned 1 of 1 pages, containing 8 live rows and 0 dead rows; 8 rows in sample, 8 estimated total rows
ANALYZE
```
Используя таблицу [pg_stats](https://postgrespro.ru/docs/postgresql/12/view-pg-stats), найдите столбец таблицы `orders` 
с наибольшим средним значением размера элементов в байтах.

**Приведите в ответе** команду, которую вы использовали для вычисления и полученный результат.
```bash
test_database=# SELECT tablename, attname, avg_width FROM pg_stats WHERE avg_width IN (SELECT MAX(avg_width) FROM pg_stats WHERE tablename = 'orders') and tablename = 'orders';
 tablename | attname | avg_width 
-----------+---------+-----------
 orders    | title   |        16
(1 row)

```
## Задача 3

Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и
поиск по ней занимает долгое время. Вам, как успешному выпускнику курсов DevOps в нетологии предложили
провести разбиение таблицы на 2 (шардировать на orders_1 - price>499 и orders_2 - price<=499).

Для шардинга таблиц мы делаем следующие шаги:

1) Переименовываем текущую таблицу в orders_old
2) Создаем пустую таблицу с исходным именем orders
3) Создаем две пустые таблицы orders_1 и orders_2 с наследованием от orders
4) Определяем в этих таблицах ограничения на значение ключа price
5) Определяем правила, при котором INSERT в таблицу orders будет заменяться на INSERT в соответствующую дочернюю таблицу (в зависимости от значения ключа price)
6) Копируем через INSERT таблицу orders_old в orders
SQL транзакция
```sql
BEGIN;
ALTER TABLE orders RENAME TO orders_old;

CREATE TABLE orders AS table orders_old WITH NO DATA;

CREATE TABLE orders_1 (
    CHECK (price > 499)
) INHERITS (orders);

CREATE TABLE orders_2 (
    CHECK (price <= 499)
) INHERITS (orders);

CREATE RULE orders_1_insert AS
ON INSERT TO orders WHERE
    (price > 499)
DO INSTEAD
    INSERT INTO orders_1 VALUES (NEW.*);
       
CREATE RULE orders_2_insert AS
ON INSERT TO orders WHERE
    (price <= 499)
DO INSTEAD
    INSERT INTO orders_2 VALUES (NEW.*);
    
INSERT INTO orders
SELECT * FROM orders_old;
COMMIT;
```

```bash
test_database=# \d
              List of relations
 Schema |     Name      |   Type   |  Owner   
--------+---------------+----------+----------
 public | orders        | table    | postgres
 public | orders_1      | table    | postgres
 public | orders_2      | table    | postgres
 public | orders_id_seq | sequence | postgres
 public | orders_old    | table    | postgres

test_database=# TABLE orders_1;
 id |       title        | price 
----+--------------------+-------
  2 | My little database |   500
  6 | WAL never lies     |   900
  8 | Dbiezdmin          |   501
(3 rows)

test_database=# TABLE orders_2;
 id |        title         | price 
----+----------------------+-------
  1 | War and peace        |   100
  3 | Adventure psql time  |   300
  4 | Server gravity falls |   300
  5 | Log gossips          |   123
  7 | Me and my bash-pet   |   499
(5 rows)

test_database=# TABLE orders;
 id |        title         | price 
----+----------------------+-------
  2 | My little database   |   500
  6 | WAL never lies       |   900
  8 | Dbiezdmin            |   501
  1 | War and peace        |   100
  3 | Adventure psql time  |   300
  4 | Server gravity falls |   300
  5 | Log gossips          |   123
  7 | Me and my bash-pet   |   499
(8 rows)


```

Можно ли было изначально исключить "ручное" разбиение при проектировании таблицы orders?

```sql
CREATE TABLE orders (
    id integer NOT NULL,
    title character varying(80) NOT NULL,
    price integer DEFAULT 0
) PARTITION BY RANGE (price);
```
Далее создаем секции orders_1 и orders_2 с указанными ограничениями
```sql
CREATE TABLE orders_1 PARTITION OF orders
    FOR VALUES GREATER THAN ('499');

CREATE TABLE orders_2 PARTITION OF orders
    FOR VALUES FROM ('0') TO ('499');
```

## Задача 4

Используя утилиту `pg_dump` создайте бекап БД `test_database`.
```bash
root@023ece8e96f1:/# pg_dump -U postgres -v -f /data/backup/postgres/test_database.sql 
```
Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца `title` для таблиц `test_database`?

Добавить ограничение `UNIQUE`
```sql
CREATE TABLE public.orders (
    id integer NOT NULL,
    title character varying(80) NOT NULL UNIQUE,
    price integer DEFAULT 0
);
```
---
