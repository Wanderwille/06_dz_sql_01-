# Домашнее задание к занятию "6.2. SQL" - Подус Сергей

## Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose манифест.

## Ответ: 

## Задача 2

В БД из задачи 1: 
- создайте пользователя test-admin-user и БД test_db
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db
- создайте пользователя test-simple-user  
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db

Таблица orders:
- id (serial primary key)
- наименование (string)
- цена (integer)

Таблица clients:
- id (serial primary key)
- фамилия (string)
- страна проживания (string, index)
- заказ (foreign key orders)

Приведите:
- итоговый список БД после выполнения пунктов выше,
- описание таблиц (describe)
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db
- список пользователей с правами над таблицами test_db

## Ответ:
- Итоговый список БД после выполнения пунктов выше

                                                                     
|   Name   |  Owner  | Encoding  | Collate    |  Ctype     |       Access privileges        |  Size   | Tablespace |                Description                |
|----------|+--------|+----------|+-----------|+-----------|+-------------------------------|+--------|+-----------|+------------------------------------------|-
|postgres  | postgres| UTF8      | en_US.utf8 | en_US.utf8 |                                | 7977 kB | pg_default | default administrative connection database|
|template0 | postgres| UTF8      | en_US.utf8 | en_US.utf8 | =c/postgres                   +| 7833 kB | pg_default | unmodifiable empty database               |
|          |         |           |            |            | postgres=CTc/postgres          |         |            |                                           |
|template1 | postgres| UTF8      | en_US.utf8 | en_US.utf8 | =c/postgres                   +| 7833 kB | pg_default | default template for new databases        |
|          |         |           |            |            | postgres=CTc/postgres          |         |            |                                           |
|test_db   | postgres| UTF8      | en_US.utf8 | en_US.utf8 | =Tc/postgres                  +| 8105 kB | pg_default |                                           |    
|          |         |           |            |            | postgres=CTc/postgres         +|         |            |                                           |
|          |         |           |            |            | "test-admin-user"=CTc/postgres |         |            |                                           | 


- описание таблиц (describe)
   
                                                                         Table "public.clients"
      Column       |          Type          | Collation | Nullable |                 Default                  | Storage  | Stats target | Description
-------------------+------------------------+-----------+----------+------------------------------------------+----------+--------------+-------------
| id                | integer                |           | not null | nextval('clients_id_seq'::regclass)      | plain    |              |
| Фамилия           | character varying(100) |           |          |                                          | extended |              |
| Страна проживания | character varying(100) |           |          |                                          | extended |              |
| Заказ             | integer                |           | not null | nextval('"clients_Заказ_seq"'::regclass) | plain    |              |             
Indexes:
    "clients_pkey" PRIMARY KEY, btree (id)
Foreign-key constraints:
    "clients_Заказ_fkey" FOREIGN KEY ("Заказ") REFERENCES orders(id)
Access method: heap

                                                           Table "public.orders"
    Column    |          Type          | Collation | Nullable |              Default               | Storage  | Stats target | Description
--------------+------------------------+-----------+----------+------------------------------------+----------+--------------+-------------
| id           | integer                |           | not null | nextval('orders_id_seq'::regclass) | plain    |              |
| Наименование | character varying(100) |           |          |                                    | extended |              |
| Цена         | integer                |           |          |                                    | plain    |              |
Indexes:
    "orders_pkey" PRIMARY KEY, btree (id)
Referenced by:
    TABLE "clients" CONSTRAINT "clients_Заказ_fkey" FOREIGN KEY ("Заказ") REFERENCES orders(id)
Access method: heap

- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db

grantor  |     grantee      | table_catalog | table_schema | table_name | privilege_type | is_grantable | with_hierarchy
----------+------------------+---------------+--------------+------------+----------------+--------------+----------------
| postgres | test-simple-user | test_db       | public       | orders     | INSERT         | NO           | NO
| postgres | test-simple-user | test_db       | public       | orders     | SELECT         | NO           | YES
| postgres | test-simple-user | test_db       | public       | orders     | UPDATE         | NO           | NO
| postgres | test-simple-user | test_db       | public       | orders     | DELETE         | NO           | NO
| postgres | test-simple-user | test_db       | public       | clients    | INSERT         | NO           | NO
| postgres | test-simple-user | test_db       | public       | clients    | SELECT         | NO           | YES
| postgres | test-simple-user | test_db       | public       | clients    | UPDATE         | NO           | NO
| postgres | test-simple-user | test_db       | public       | clients    | DELETE         | NO           | NO
(8 rows)

- список пользователей с правами над таблицами test_db

Таблица Orders:

 postgres         | INSERT
 postgres         | SELECT
 postgres         | UPDATE
 postgres         | DELETE
 postgres         | TRUNCATE
 postgres         | REFERENCES
 postgres         | TRIGGER
 test-simple-user | INSERT
 test-simple-user | SELECT
 test-simple-user | UPDATE
 test-simple-user | DELETE

Таблица Clients:

 postgres         | INSERT
 postgres         | SELECT
 postgres         | UPDATE
 postgres         | DELETE
 postgres         | TRUNCATE
 postgres         | REFERENCES
 postgres         | TRIGGER
 test-simple-user | INSERT
 test-simple-user | SELECT
 test-simple-user | UPDATE
 test-simple-user | DELETE

