# OTUS MySQL Docker

## Описание

В рамках домашнего задания был развернут контейнер MySQL в Docker.

SQL-скрипт инициализации базы данных помещен в файл `init.sql`.
При старте контейнера автоматически создается база данных `otus`, таблицы и тестовые данные.

## Выполнение задания

### 1. Прописан SQL-скрипт для создания своей БД в `init.sql`

В файле `init.sql` описано создание базы данных `otus`, таблиц и тестовых данных.

Созданы таблицы:

- categories
- suppliers
- manufacturers
- products
- customers
- prices
- purchases

### 2. Проверен запуск и работа контейнера по описанию из репозитория

Поднимаем сервис и подключаемся:

```
cd ./otus-mysql-docker
docker-compose up -d
docker-compose exec otusdb mysql --default-character-set=utf8mb4 -u root -p12345 otus
```

Проверяем наличие базы данных:
```
SHOW DATABASES;
```

<img width="263" height="242" alt="image" src="https://github.com/user-attachments/assets/71a762c2-82d5-4ca5-bb69-2ca9927e81b0" />

Проверяем таблицы:
```
USE otus;
SHOW TABLES;
```

<img width="251" height="250" alt="image" src="https://github.com/user-attachments/assets/cdeb4ac3-83f7-497e-8a54-b4452a59380f" />


Кастомный конфиг MySQL

Для получения дополнительных баллов был добавлен кастомный конфиг в файл:

`custom.conf/my.cnf`

Использованы параметры:
```
[mysqld]
default-authentication-plugin=mysql_native_password

innodb_buffer_pool_size=256M
innodb_log_file_size=64M
max_connections=100
secure-file-priv=/var/lib/mysql-files

character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci
```

Проверка применения параметров:
```
docker-compose exec otusdb mysql --default-character-set=utf8mb4 -u root -p12345 otus


SHOW VARIABLES LIKE 'innodb_buffer_pool_size';
SHOW VARIABLES LIKE 'innodb_log_file_size';
SHOW VARIABLES LIKE 'max_connections';
SHOW VARIABLES LIKE 'character_set_server';
SHOW VARIABLES LIKE 'collation_server';
```

Структура базы данных

`categories`

Таблица категорий товаров.

`suppliers`

Таблица поставщиков.

`manufacturers`

Таблица производителей.

`products`

Таблица товаров.
Связана с категориями, поставщиками и производителями.

`customers`

Таблица покупателей.

`prices`

Таблица цен на товары.

`purchases`

Таблица покупок.
Связана с покупателями, товарами и ценами.

Проверка данных

Примеры запросов:

```
SELECT * FROM categories;
SELECT * FROM suppliers;
SELECT * FROM manufacturers;
SELECT * FROM products;
SELECT * FROM customers;
SELECT * FROM prices;
SELECT * FROM purchases;
```

Тестирование с помощью sysbench

Подготовка тестовых данных
```
sysbench /usr/share/sysbench/oltp_read_write.lua \
  --db-driver=mysql \
  --mysql-host=127.0.0.1 \
  --mysql-port=3309 \
  --mysql-user=root \
  --mysql-password=12345 \
  --mysql-db=otus \
  --tables=2 \
  --table-size=10000 \
  --threads=4 \
  prepare
```

<img width="483" height="182" alt="image" src="https://github.com/user-attachments/assets/a30b25d0-c9a8-4ca9-81cd-88d561f74fed" />


Запуск теста
```
sysbench /usr/share/sysbench/oltp_read_write.lua \
  --db-driver=mysql \
  --mysql-host=127.0.0.1 \
  --mysql-port=3309 \
  --mysql-user=root \
  --mysql-password=12345 \
  --mysql-db=otus \
  --tables=2 \
  --table-size=10000 \
  --threads=4 \
  --time=60 \
  run
```

Результат теста
```
SQL statistics:
    queries performed:
        read:                            156520
        write:                           44693
        other:                           22349
        total:                           223562
    transactions:                        11169  (186.08 per sec.)
    queries:                             223562 (3724.59 per sec.)
    ignored errors:                      11     (0.18 per sec.)
    reconnects:                          0      (0.00 per sec.)

General statistics:
    total time:                          60.0215s
    total number of events:              11169

Latency (ms):
         min:                                   10.65
         avg:                                   21.48
         max:                                   57.73
         95th percentile:                       26.68
         sum:                               239865.51

Threads fairness:
    events (avg/stddev):           2792.2500/4.44
    execution time (avg/stddev):   59.9664/0.00
```

Очистка тестовых данных
```
sysbench /usr/share/sysbench/oltp_read_write.lua \
  --db-driver=mysql \
  --mysql-host=127.0.0.1 \
  --mysql-port=3309 \
  --mysql-user=root \
  --mysql-password=12345 \
  --mysql-db=otus \
  --tables=2 \
  --table-size=10000 \
  cleanup
```

Итог

В рамках домашнего задания выполнено:

- развернут контейнер MySQL в Docker;
- создан SQL-скрипт инициализации базы данных в init.sql;
- проверен запуск контейнера и создание базы otus;
- реализован кастомный конфиг MySQL;
- выполнено тестирование с помощью sysbench;
- результаты тестирования добавлены в README.md.
