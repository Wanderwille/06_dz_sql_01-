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

                                                                     
|   Name    |  Owner  | Encoding  | Collate    |  Ctype     |       Access privileges         |  Size   | Tablespace |                Description                |
|-----------|---------|-----------|------------|------------|---------------------------------|---------|------------|-------------------------------------------|
| postgres  | postgres| UTF8      | en_US.utf8 | en_US.utf8 |                                 | 7977 kB | pg_default | default administrative connection database|
| template0 | postgres| UTF8      | en_US.utf8 | en_US.utf8 | =c/postgres                   + | 7833 kB | pg_default | unmodifiable empty database               |
|           |         |           |            |            | postgres=CTc/postgres           |         |            |                                           |
| template1 | postgres| UTF8      | en_US.utf8 | en_US.utf8 | =c/postgres                   + | 7833 kB | pg_default | default template for new databases        |
|           |         |           |            |            | postgres=CTc/postgres           |         |            |                                           |
| test_db   | postgres| UTF8      | en_US.utf8 | en_US.utf8 | =Tc/postgres                  + | 8105 kB | pg_default |                                           |    
|           |         |           |            |            | postgres=CTc/postgres         + |         |            |                                           |
|           |         |           |            |            | "test-admin-user"=CTc/postgres  |         |            |                                           | 


- описание таблиц (describe)
   
                                                                         Table "public.clients"
|     Column        |          Type          | Collation | Nullable |                 Default                  | Storage  | Stats target | Description |
|-------------------|------------------------|-----------|----------|------------------------------------------|----------|--------------|-------------|
| id                | integer                |           | not null | nextval('clients_id_seq'::regclass)      | plain    |              |             |
| Фамилия           | character varying(100) |           |          |                                          | extended |              |             |
| Страна проживания | character varying(100) |           |          |                                          | extended |              |             |
| Заказ             | integer                |           | not null | nextval('"clients_Заказ_seq"'::regclass) | plain    |              |             |
Indexes:
    "clients_pkey" PRIMARY KEY, btree (id)
Foreign-key constraints:
    "clients_Заказ_fkey" FOREIGN KEY ("Заказ") REFERENCES orders(id)
Access method: heap

                                                           Table "public.orders"
|    Column    |          Type          | Collation | Nullable |              Default               | Storage  | Stats target | Description|
|--------------|------------------------|-----------|----------|------------------------------------|----------|--------------|------------|
| id           | integer                |           | not null | nextval('orders_id_seq'::regclass) | plain    |              |            |
| Наименование | character varying(100) |           |          |                                    | extended |              |            |
| Цена         | integer                |           |          |                                    | plain    |              |            |
Indexes:
    "orders_pkey" PRIMARY KEY, btree (id)
Referenced by:
    TABLE "clients" CONSTRAINT "clients_Заказ_fkey" FOREIGN KEY ("Заказ") REFERENCES orders(id)
Access method: heap

- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db

`SELECT * FROM information_schema.table_privileges WHERE grantee = 'test-simple-user' OR grantee = 'test-admin-user';`  

| grantor  |     grantee      | table_catalog | table_schema | table_name | privilege_type | is_grantable | with_hierarchy|
|----------|------------------|---------------|--------------|------------|----------------|--------------|---------------|
| postgres | test-simple-user | test_db       | public       | orders     | INSERT         | NO           | NO            |
| postgres | test-simple-user | test_db       | public       | orders     | SELECT         | NO           | YES           |
| postgres | test-simple-user | test_db       | public       | orders     | UPDATE         | NO           | NO            |
| postgres | test-simple-user | test_db       | public       | orders     | DELETE         | NO           | NO            |
| postgres | test-simple-user | test_db       | public       | clients    | INSERT         | NO           | NO            |
| postgres | test-simple-user | test_db       | public       | clients    | SELECT         | NO           | YES           |
| postgres | test-simple-user | test_db       | public       | clients    | UPDATE         | NO           | NO            |
| postgres | test-simple-user | test_db       | public       | clients    | DELETE         | NO           | NO            |
(8 rows)

- список пользователей с правами над таблицами test_db

Таблица Orders:

| Grantor          | Privilege |
|------------------|-----------|
| postgres         | INSERT    |
| postgres         | SELECT    |
| postgres         | UPDATE    |
| postgres         | DELETE    |
| postgres         | TRUNCATE  |
| postgres         | REFERENCES|
| postgres         | TRIGGER   |
| test-simple-user | INSERT    |
| test-simple-user | SELECT    |
| test-simple-user | UPDATE    |
| test-simple-user | DELETE    |

Таблица Clients:
| Grantor          | Privilege |
|------------------|-----------|
| postgres         | INSERT    |
| postgres         | SELECT    |
| postgres         | UPDATE    |
| postgres         | DELETE    |
| postgres         | TRUNCATE  |
| postgres         | REFERENCES|
| postgres         | TRIGGER   |
| test-simple-user | INSERT    |
| test-simple-user | SELECT    |
| test-simple-user | UPDATE    |
| test-simple-user | DELETE    |

## Задача 3

Используя SQL синтаксис - наполните таблицы следующими тестовыми данными:

Таблица orders

|Наименование|цена|
|------------|----|
|Шоколад| 10 |
|Принтер| 3000 |
|Книга| 500 |
|Монитор| 7000|
|Гитара| 4000|

Таблица clients

|ФИО|Страна проживания|
|------------|----|
|Иванов Иван Иванович| USA |
|Петров Петр Петрович| Canada |
|Иоганн Себастьян Бах| Japan |
|Ронни Джеймс Дио| Russia|
|Ritchie Blackmore| Russia|

Используя SQL синтаксис:
- вычислите количество записей для каждой таблицы 
- приведите в ответе:
    - запросы 
    - результаты их выполнения.


## Ответ: 

Запросы: 

## SQL-запросы:   
```
INSERT INTO public.orders(id,Наименование,Цена) VALUES (0,NULL,0);
INSERT INTO public.orders(Наименование,Цена) VALUES ('Шоколад',10);
INSERT INTO public.orders(Наименование,Цена) VALUES('Принтер',3000);
INSERT INTO public.orders(Наименование,Цена) VALUES('Книга',500);
INSERT INTO public.orders(Наименование,Цена) VALUES('Монитор',7000);
INSERT INTO public.orders(Наименование,Цена) VALUES('Гитара',4000);

INSERT INTO public.clients("Фамилия", "Страна проживания", "Заказ") VALUES('Иванов Иван Иванович','USA', 0);
INSERT INTO public.clients("Фамилия", "Страна проживания", "Заказ") VALUES('Петров Петр Петрович','Canada', 0);
INSERT INTO public.clients("Фамилия", "Страна проживания", "Заказ") VALUES('Иоганн Себастьян Бах','Japan', 0);
INSERT INTO public.clients("Фамилия", "Страна проживания", "Заказ") VALUES('Ронни Джеймс Дио','Russia', 0);	
INSERT INTO public.clients("Фамилия", "Страна проживания", "Заказ") VALUES('Ritchie Blackmore','Russia', 0);		
```

Для добавления данных в таблицу "public.clients", пришлось внести значение 0, для того что бы "завести" пользователей. 

- вычислите количество записей для каждой таблицы:

SELECT COUNT(*) FROM public.orders;

| count   |
| ------- |
|      6  |  
| (1 row) |

SELECT COUNT(*) FROM public.clients;

| count   |
| ------- |
|      5  |  
| (1 row) |


## Задача 4 

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения данных операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.
 
Подсказка - используйте директиву `UPDATE`.

## Ответ:

## SQL-запросы для выполнения данных операций:

```
UPDATE public.clients SET "Заказ"=(SELECT id FROM public.orders WHERE "Наименование"='Книга') 
WHERE Фамилия='Иванов Иван Иванович';   
UPDATE public.clients SET "Заказ"=(SELECT id FROM public.orders WHERE "Наименование"='Монитор') 
WHERE Фамилия='Петров Петр Петрович';   
UPDATE public.clients SET "Заказ"=(SELECT id FROM public.orders WHERE "Наименование"='Гитара') 
WHERE Фамилия='Иоганн Себастьян Бах';   
```

## SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса:  
```
SELECT C."Фамилия", C."Страна проживания", O."Наименование" Товар  FROM public.clients C
INNER JOIN public.orders O ON C.Заказ=O.id
WHERE O.id != 0;
```

|       Фамилия        | Страна проживания |  Товар  |
|----------------------|-------------------|---------|
| Иванов Иван Иванович | USA               | Книга   |
| Петров Петр Петрович | Canada            | Монитор |
| Иоганн Себастьян Бах | Japan             | Гитара  |

(3 rows)

## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните что значат полученные значения.

## Ответ:

```
EXPLAIN ANALYZE 
SELECT C."Фамилия", C."Страна проживания", O."Наименование" Товар  
FROM public.clients C 
INNER JOIN public.orders O ON C.Заказ=O.id 
WHERE O.id != 0;
```
``` 
                                                    QUERY PLAN
-------------------------------------------------------------------------------------------------------------------
 Hash Join  (cost=17.99..30.14 rows=169 width=654) (actual time=0.022..0.024 rows=3 loops=1)
   Hash Cond: (c."Заказ" = o.id)
   ->  Seq Scan on clients c  (cost=0.00..11.70 rows=170 width=440) (actual time=0.006..0.007 rows=5 loops=1)
   ->  Hash  (cost=14.00..14.00 rows=319 width=222) (actual time=0.010..0.010 rows=5 loops=1)
         Buckets: 1024  Batches: 1  Memory Usage: 9kB
         ->  Seq Scan on orders o  (cost=0.00..14.00 rows=319 width=222) (actual time=0.005..0.006 rows=5 loops=1)
               Filter: (id <> 0)
               Rows Removed by Filter: 1
 Planning Time: 0.088 ms
 Execution Time: 0.041 ms
(10 rows)
``` 

Seq Scan on clients (Последовательное сканирование таблицы clients):

cost=0.00..11.70 rows=170 width=440: Это стоимость выполнения операции. В данном случае, PostgreSQL решает использовать последовательное сканирование (Seq Scan) для таблицы "clients". Ожидаемое количество строк - 170, ширина строки - 440 байт.

Актуальное время "ctual time=0.006..0.007", 5 строк 

Hash (Хеш):

cost=14.00..14.00 rows=319 width=222: Это создание хеша для соединения. PostgreSQL создает хеш-таблицу для столбца "id" в таблице "orders". 

Execution Time: 0.041 ms - Время выполнения запроса. 

