1) create table category
(
    id   SERIAL constraint category_pk
            primary key,
    name varchar
);

create table product
(
    id          serial
        constraint product_pk
            primary key,
    name        varchar,
    category_id integer
        constraint category_id_fk
            references category
);
---------------
insert into category values (1, 'Категория 1');
insert into category values (2, 'Категория 2');
insert into category values (3, 'Категория 3');

insert into product values (1, 'Товар 1', 1);
insert into product values (2, 'Товар 2', 2);
insert into product values (3, 'Товар 3', 3);

insert into product values (4, 'Товар 4', 1);
insert into product values (5, 'Товар 5', 2);
insert into product values (6, 'Товар 6', 3);
insert into product values (7, 'Товар 7', 1);
insert into product values (8, 'Товар 8', 2);
insert into product values (9, 'Товар 9', 3);

напиши запрос на получение получение  product.id, product.name, category.id, category.name, где category.id = 2 или 3 (отдельно через sql, отдельно через sqlalchemy

—------------------------------------------------------------------------------------------------------------------------

2) напиши запрос который выведет все департаменты с более чем двумя сотрудниками
CREATE TABLE department
(
  id int(11),
  name varchar(100)
);

CREATE TABLE employee
(
  id int(11),
  department_id int(11),
  chief_id int(11),
  name varchar(100),
  salary int(11)
);

INSERT INTO department
(id, name)
VALUES
(1, "D1"),
(2, "D2"),
(3, "D3");

INSERT INTO employee
(id, department_id, chief_id, name, salary)
VALUES
(1, 1, 5, "John", 100),
(2, 1, 5, "Misha", 600),
(3, 2, 5, "Eugen", 300),
(4, 2, 5, "Tolya", 400),
(5, 1, NULL, "Stepan Chief", 500);

-- вывести department, где больше двух employee


—------------------------------------------------------------------------------------------------------------------------
3) Написать SQL запрос использующий операторы SELECT, FROM, JOIN, WHERE, GROUP BY, HAVING, ORDER BY.

—------------------------------------------------------------------------------------------------------------------------

4) Анализ подзапроса: есть ли в нем ошибки и насколько он производителен
— Что вы можете рассказать об этом подзапросе? Есть ли в нем что-то неправильное? Насколько он будет производительно работать?
SELECT E.*
  FROM Employee E
 WHERE EXISTS (
  SELECT *
	FROM Department D
   WHERE D.DepartmentID = E.DepartmentID
  );


—------------------------------------------------------------------------------------------------------------------------

5) Какие из запросов/подзапросов, приведенных ниже, являются коррелированными, а какие некоррелированными? 
Запрос 1:
SELECT E.* FROM Employee E
 WHERE EXISTS (
   SELECT *FROM Department D
	WHERE D.DepartmentID = E.DepartmentID;

Запрос 2:
SELECT E.* FROM Employee E
 WHERE E.DepartmentID IN (
	SELECT DepartmentID
  	FROM Department D);

Запрос 3:
SELECT E.* FROM Employee E
JOIN Department D ON (E.DepartmentID = D.DepartmentID);


—------------------------------------------------------------------------------------------------------------------------
6) Оптимизация запроса с LIMIT и OFFSET на большой таблице
— Таблица movies содержит огромное количество тяжеловесных полей. Таблица имеет первичный ключ в поле id, заданный целочисленными идентификаторами. Идентификаторы заданы случайно, не автоинкрементные. Запрос тормозит. Можно ли его как-то оптимизировать?

SELECT *
  FROM movies
  LIMIT 10 OFFSET 100;

—------------------------------------------------------------------------------------------------------------------------

7) Какие проблемы могут возникнуть с этими операциями в коде ниже, если убрать FOR UPDATE?
Транзакция 1:
BEGIN;
INSERT INTO spent_daily
SELECT :day, level, SUM(spent_daily) FROM players
GROUP BY level FOR UPDATE;
UPDATE players SET spent_daily = 0;
END;
Транзакция 2:
UPDATE players
   SET spent_daily = spent_daily + :delta,
       money = money - :delta
 WHERE id = :id;

—------------------------------------------------------------------------------------------------------------------------


8) Анализ запросов с блокировками
— Расскажите о запросах ниже 
Запрос 1:
BEGIN;
SELECT * FROM player WHERE id = 42 FOR UPDATE;
...
UPDATE player SET money = 100500 WHERE id = 42;
COMMIT;
Запрос 2:
BEGIN;
SELECT * FROM player WHERE id = 42;
COMMIT;
...
BEGIN;
UPDATE player SET money = 100500, ver = 13
WHERE id = 42 AND ver = 12;
COMMIT;

—------------------------------------------------------------------------------------------------------------------------

9)  Запрос выполняется по 40 минут и подвешивает всё приложение. Как уменьшить время блокировки?

DELETE FROM work.logs
 WHERE created_at > NOW() - interval '90 days';

—------------------------------------------------------------------------------------------------------------------------

10) Оптимизация массовых UPDATE запросов
— У нас есть около 30 тысяч инструкций, приведенных ниже. Можно ли их как-то оптимизировать?

UPDATE items SET res_id = 73534, level = 1, meta = 1001
 WHERE res_id = 40477;
UPDATE items SET res_id = 73534, level = 1, meta = 1201
 WHERE res_id = 40478;
UPDATE items SET res_id = 73534, level = 2, meta = 1031
 WHERE res_id = 40479;
...
UPDATE items SET res_id = 73534, level = 80, meta = 7641
 WHERE res_id = 70477;

—------------------------------------------------------------------------------------------------------------------------

11) — Транзакция выполняется долго. Как ограничить транзакцию по времени ожидания?

BEGIN;
  UPDATE player SET money = 100500 WHERE id = 42;
  COMMIT;

—------------------------------------------------------------------------------------------------------------------------


12) Опишите конструкции в PostgreSQL и их действия
— Что делают следующие конструкции в PostgreSQL? 

Вариант 1:

CREATE TABLE test (
	id SERIAL PRIMARY KEY,
	title TEXT,
	created_on TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
CREATE INDEX idx_test_created_on ON test (created_on);
CREATE TABLE test_1
	(CHECK (id >= 100 AND id < 200))
	INHERITS (test);
CREATE TABLE test_2 (LIKE test INCLUDING ALL);
ALTER TABLE test_2
   INHERIT test,
   ADD CONSTRAINT partition_check CHECK (id >= 200 and id < 300);

Вариант 2:

CREATE TABLE test (
	id SERIAL PRIMARY KEY,
	title TEXT,
	created_on TIMESTAMPTZ NOT NULL DEFAULT NOW()
) PARTITION BY RANGE (id);
CREATE TABLE test_1 PARTITION OF test FOR VALUES FROM (100) TO (199);
CREATE TABLE test_2 PARTITION OF test FOR VALUES FROM (200) TO (299);

—------------------------------------------------------------------------------------------------------------------------

13) — На какие из таблиц в варианте 1 будут созданы индексы?
— Мы выполняем следующие операции:

INSERT INTO test   (id) VALUES (1);
INSERT INTO test_1 (id) VALUES (100);
INSERT INTO test_2 (id) VALUES (200);

— Что вернут следующие запросы?
SELECT * FROM test;
SELECT * FROM test_1;
SELECT * FROM test_2;

—------------------------------------------------------------------------------------------------------------------------

14) Вывести уникальные комбинации пользователя и id товара для всех покупок, совершенных пользователями до того, как их забанили.
Отсортировать сначала по имени пользователя, потом по SKU
// user
id | firstname | lastname | birth
1  | Ivan  	| Petrov   | 1996-05-01
2  | Anna  	| Petrova  | 1999-06-01 	 
3  | Anna  	| Petrova  | 1990-10-02
 	 
// purchase
sku| price | user_id | date
1  | 5500  | 1   	| 2021-02-15
1  | 5700  | 1   	| 2021-01-15
2  | 4000  | 1   	| 2021-02-14
3  | 8000  | 2   	| 2021-03-01
4  | 400   | 2   	| 2021-03-02

// ban_list
user_id | date_from   
1   	| 2021-03-08
—------------------------------------------------------------------------------------------------------------------------
 
15) Нужно описать модель библиотеки. Есть 3 сущности: "Автор", "Книга", "Читатель"
Физически книга только одна и может быть только у одного читателя. Нужно составить таблицы для библиотеки так что бы это учесть.
--Q: Написать запрос - выбрать названия всех книг которые на руках
--Q: Написать запрос - выбрать названия всех книг в библиотеке у которых больше 3 авторов
-- Q: Написать запрос - выбрать имена топ 3 читаемых авторов на данный момент

—------------------------------------------------------------------------------------------------------------------------

16) Есть таблица на 1 миллиард записей, которая активно используется в продашкне. Нужно сделать датафикс, который модифицирует 10 миллионов строк.
Как бы ты подошел к решению задачи?

—------------------------------------------------------------------------------------------------------------------------

17) 

-- Пользователи
CREATE TABLE users (
    id ROWID, 
    name TEXT, 
    email TEXT
);

-- Списки задач
CREATE TABLE task_list (
    id ROWID,
    user_id INTEGER,
    title TEXT
);

-- Отдельные элементы в списке задач
CREATE TABLE task_list_item (
    id ROWID,
    task_list_id INTEGER,
    title TEXT
);

--
-- Демо данные
--
INSERT INTO users (id, name, email)
VALUES
    (1, 'Петр', 'petr@example.com'),
    (2, 'Иван', 'ivan@example.com'),
    (3, 'Иван', 'ivan.pastuhov@example.com'),
    (4, 'Сергей', 's.losev@example.com');

INSERT INTO task_list (id, user_id, title)
VALUES
    (1, 1, 'TODO'),
    (2, 2, 'Надо сделать'),
    (3, 3, 'TOTO список'),
    (4, 2, 'Покупки'),
    (5, 1, 'Купить'),
    (6, 9, 'Список дел');


INSERT INTO task_list_item (id, task_list_id, title)
VALUES 
    (1, 1, 'Прочитать A'),
    (2, 1, 'Прочитать B'),
    (3, 1, 'Прочитать C'),
    (4, 2, 'Прочитать D'),
    (5, 4, 'Купить A'),
    (6, 5, 'Купить B'),
    (7, 5, 'Купить C'),
    (8, 5, 'Купить A'),
    (9, 5, 'Купить X');

-- вывести пользователей и их списки
-- вывести всех пользователей, их списки и число записей в каждом списке
-- Вывести списки не привязанные ни ко одному пользователю

—------------------------------------------------------------------------------------------------------------------------

18) Поиск SQL-инъекции в raw SQL и handler
— Дали raw SQL и обработчик. Нужно было найти SQL-инъекцию и понять, что делает запрос.

—------------------------------------------------------------------------------------------------------------------------

19) есть таблица 10 строк - 10 индексов
надо вставить миллион строк, а потом тут же эффективно с индексами с этими данными повозиться
как сделать* 

—------------------------------------------------------------------------------------------------------------------------

20)  5 миллионов записей о людях. Время от времени надо делать SELECT всех мужчин или SELECT всех женщин, коих поровну. Как это сделать чуть быстрее? *
