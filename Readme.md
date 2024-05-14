Лабораторная работа №6 по курсу NoSQL.

Задание:
	- Развернуть docker локально или в облаке
	- Поднять 3 узловый Cassandra кластер.
	- Создать keyspase с 2-мя таблицами. Одна из таблиц должна иметь составной Partition key, как минимум одно поле - 			clustering key, как минимум одно поле не входящее в primiry key.
	- Заполнить данными обе таблицы.
	- Выполнить 2-3 варианта запроса использую WHERE
	- Создать вторичный индекс на поле, не входящее в primiry key.
	- (*) нагрузить кластер при помощи Cassandra Stress Tool (используя "How to use Apache Cassandra Stress Tool.pdf" из материалов).

Решение:
1) Развернуто 3 виртуальные машины, на которые установлена СУБД Cassandra.
2) Поднят кластер cas_cluster, состоящий из 3-х нод (см рисунок 1)
3) Создан keyspase example
	create keyspace example with replication ={'class':'SimpleStrategy','replication_factor':2};
4) В keyspace создано две таблицы:
	- create table my_table ( id int primary key, description text );
	- create table my_table_new ( id uuid, num int, descr text, primary key(id, num) ) with clustering order by (num ASC) ;
5) В таблицы помещены данные (по 5 строк в каждую)
6) Выборка из таблицы my_table (см рисунок 2)
7) Построен вторичный индекс для поля description таблицы my_table (для фильтрации по полю description, см рисунок 3)
	create index on my_table (description);
8) Таблица my_table_new создана составным первичным ключом (primary key(id, num)) и кластерным ключом. Выборка данных из этой таблицы (см рисунок 4)
9) Запущено приложение Cassandra Stress Tool, на запись отправлено 200 000 строк. Запись заняла 33 секунды, средняя задержка на запись 8мс (см рисунок 5). Аналогично протестировано чтение, результат 34 секунды и задержка 9мс.
Параметры запуска: cassandra-stress write n=200000 -rate threads=5 -node 192.168.50.154
