﻿---
layout: default
title: Проверка наличия логической сущности
nav_order: 14
parent: Управление схемой данных
grand_parent: Работа с системой
has_children: false
---

# Проверка наличия логической сущности

При успешном создании любой сущности — [логической базы данных](../../../overview/main_concepts/logical_db/logical_db.md), 
[логической таблицы](../../../overview/main_concepts/logical_table/logical_table.md), 
[логического представления](../../../overview/main_concepts/logical_view/logical_view.md),
[внешней таблицы](../../../overview/main_concepts/external_table/external_table.md) 
загрузки, внешней таблицы выгрузки или [материализованного представления](../../../overview/main_concepts/materialized_view/materialized_view.md) 
— система возвращает пустой объект ResultSet в ответе на соответствующий запрос. Если сущность не удалось 
создать из-за ошибки, система возвращает исключение в ответе. Таким образом, по результатам выполнения запроса 
можно определить, создалась ли нужная сущность. Однако при необходимости можно проверить ее наличие, 
как описано в этом разделе.

Проверить наличие логической сущности можно несколькими способами:
* [запросить метаданные логической схемы данных](../request_from_schema/request_from_schema.md), 
  выполнив запрос к соответствующему [системному представлению](../../../reference/system_views/system_views.md) 
  (способ недоступен для внешних таблиц);
* выполнить [SELECT](../../../reference/sql_plus_requests/SELECT/SELECT.md)-запрос к 
  логической сущности (способ недоступен для логической БД и внешних таблиц);
* проверить дерево объектов в SQL-клиенте.

Примеры запросов для каждой из сущностей приведены в секциях ниже:
* [Проверка наличия логической базы данных](#проверка-наличия-логической-базы-данных);
* [Проверка наличия логической таблицы](#проверка-наличия-логической-таблицы);
* [Проверка наличия логического представления](#проверка-наличия-логического-представления);
* [Проверка наличия материализованного представления](#проверка-наличия-материализованного-представления);
* [Проверка наличия внешней таблицы](#проверка-наличия-внешней-таблицы).

**Примечание:** наличие внешних таблиц загрузки и выгрузки можно проверить только в дереве объектов 
SQL-клиента. Внешние таблицы не отображаются в системных представлениях, и для них недоступны SELECT-запросы.

## Проверка наличия логической базы данных

Чтобы проверить наличие логической базы данных, используйте любой из способов:
*   Выполните запрос к системному представлению `schemata` (вместо `DB_NAME` подставьте имя логической БД в 
    верхнем регистре; см. пример [ниже](#ex_db)):
    ```sql
    SELECT * FROM INFORMATION_SCHEMA.schemata
    WHERE schema_name = '<DB_NAME>'
    ```
    Если логическая база данных существует, запрос возвращает одну запись, иначе — ноль записей.
*   Проверьте дерево объектов в вашем SQL-клиенте (см. рисунок [ниже](#img_table_in_tree)).
    <br>Если логическая база данных существует, она присутствует среди объектов в SQL-клиенте,
    иначе — отсутствует среди объектов.

В примере ниже показан запрос для проверки наличия логической БД `sales`.

<a id="ex_db"></a>
``` sql
-- проверка наличия логической базы данных sales
SELECT
  CASE
    WHEN count(*) > 0 THEN 'БД существует'
    ELSE 'БД не существует'
  END
FROM INFORMATION_SCHEMA.schemata
WHERE schema_name = 'SALES'
```

На рисунке ниже показана логическая БД в дереве объектов SQL-клиента.

<a id="img_table_in_tree"></a>
![](db_in_tree.png){:height="50%" width="50%"}
{: .figure-center}
*Логическая БД в дереве объектов*
{: .figure-caption-center}

## Проверка наличия логической таблицы

Чтобы проверить наличие логической таблицы, используйте любой из способов:
*   Выполните запрос к системному представлению `tables` (вместо `DB_NAME` и `TABLE_NAME` подставьте имя логической БД и имя
    таблицы в верхнем регистре; см. пример [ниже](#ex_table)):
    ```sql
    SELECT * FROM INFORMATION_SCHEMA.tables
    WHERE table_schema = '<DB_NAME>' AND (table_name = '<TABLE_NAME>' AND table_type = 'BASE TABLE')
    ```
    Если логическая таблица существует, запрос возвращает одну запись, иначе — ноль записей.
*   Выполните [SELECT](../../../reference/sql_plus_requests/SELECT/SELECT.md)-запрос к логической
    таблице, например:
    ```sql
    SELECT * FROM <db_name>.<table_name> LIMIT 5
    ```
    Если логическая таблица существует, запрос возвращает от ноля до пяти записей (в зависимости от содержимого
    таблицы), иначе — исключение `Entity <table_name> does not exist`.
*   Проверьте дерево объектов в вашем SQL-клиенте (см. рисунок [ниже](#img_table_in_object_tree)).
    <br>Если логическая таблица существует, она присутствует среди объектов логической БД в SQL-клиенте,
    иначе — отсутствует среди объектов логической БД.
    
В примере ниже показан запрос для проверки наличия логической таблицы `sales`.

<a id="ex_table"></a>
```sql
-- проверка наличия логической таблицы sales
SELECT
  CASE
    WHEN count(*) > 0 THEN 'таблица существует'
    ELSE 'таблица не существует'
  END
FROM INFORMATION_SCHEMA.tables
WHERE table_schema = 'SALES' AND (table_name = 'SALES' AND table_type = 'BASE TABLE')
```

На рисунке ниже показано дерево объектов SQL-клиента, которое содержит логические таблицы `sales` и `stores`.

<a id="img_table_in_object_tree"></a>
![](table_in_tree.png){:height="40%" width="40%"}
{: .figure-center}
*Логические таблицы в дереве объектов*
{: .figure-caption-center}

## Проверка наличия логического представления

Чтобы проверить наличие логического представления, используйте любой из способов:
*   Выполните запрос к системному представлению `tables` (вместо `DB_NAME` и `VIEW_NAME` подставьте имя логической БД и имя
    логического представления в верхнем регистре; см. пример [ниже](#ex_view)):
    ```sql
    SELECT * FROM INFORMATION_SCHEMA.tables
    WHERE table_schema = '<DB_NAME>' AND (table_name = '<VIEW_NAME>' AND table_type = 'VIEW')
    ```
    Если логическое представление существует, запрос возвращает одну запись, иначе — ноль записей.
*   Выполните [SELECT](../../../reference/sql_plus_requests/SELECT/SELECT.md)-запрос
    к логическому представлению, например:
    ```sql
    SELECT * FROM <db_name>.<view_name> LIMIT 5
    ```
    Если логическое представление существует, запрос возвращает от ноля до пяти записей (в зависимости от
    содержимого представления), иначе — исключение `Entity <view_name> does not exist`.
*   Проверьте дерево объектов в вашем SQL-клиенте (см. рисунок [ниже](#img_view_in_object_tree)).
    <br>Если логическое представление, оно присутствует среди объектов логической БД в SQL-клиенте,
    иначе — отсутствует среди объектов логической БД.

В примере ниже показан запрос для проверки наличия логического представления `stores_by_sold_products`.

<a id="ex_view"></a>
```sql
-- проверка наличия логического представления stores_by_sold_products
SELECT
  CASE
    WHEN count(*) > 0 THEN 'представление существует'
    ELSE 'представление не существует'
  END
FROM INFORMATION_SCHEMA.tables
WHERE table_schema = 'SALES' AND (table_name = 'STORES_BY_SOLD_PRODUCTS' AND table_type = 'VIEW')
```

На рисунке ниже показан фрагмент дерева объектов SQL-клиента, которое содержит логическое представление
`stores_by_sold_products`.

<a id="img_view_in_object_tree"></a>
![](view_in_tree.png){:height="50%" width="50%"}
{: .figure-center}
*Логическое представление в дереве объектов*
{: .figure-caption-center}

## Проверка наличия материализованного представления

Чтобы проверить наличие материализованного представления, используйте любой из способов:
*   Выполните запрос к системному представлению `tables` (вместо `DB_NAME` и `MATERIALIZED_VIEW_NAME` подставьте имя логической БД и имя
    представления в верхнем регистре; см. пример [ниже](#ex_mat_view)):
    ```sql
    SELECT * FROM INFORMATION_SCHEMA.tables
    WHERE table_schema = '<DB_NAME>' AND
      ((table_name = '<MATERIALIZED_VIEW_NAME>' AND table_type = 'BASE TABLE') OR
       (table_name = 'SYS_<MATERIALIZED_VIEW_NAME>' AND table_type = 'VIEW'))
    ```
    Если материализованное представление существует, запрос возвращает две записи, иначе — ноль записей.
*   Выполните [SELECT](../../../reference/sql_plus_requests/SELECT/SELECT.md)-запрос к материализованному
    представлению, например:
    ```sql
    SELECT * FROM <db_name>.<materialized_view_name> LIMIT 5
    ```
    Если материализованное представление существует, запрос возвращает от ноля до пяти записей (в зависимости от
    содержимого представления), иначе — исключение `Entity <materialized_view_name> does not exist`.
*   Проверьте дерево объектов в вашем SQL-клиенте (см. рисунок [ниже](#img_mat_view_in_object_tree)).
    <br>Если материализованное представление существует, оно присутствует среди объектов логической БД в SQL-клиенте,
    иначе — отсутствует среди объектов логической БД.

В примере ниже показан запрос для проверки наличия материализованного представления `sales_and_stores`.

<a id="ex_mat_view"></a>
```sql
-- проверка наличия материализованного представления sales_and_stores
SELECT
  CASE
    WHEN count(*) > 1 THEN 'представление существует'
    ELSE 'представление не существует'
  END
FROM INFORMATION_SCHEMA.tables
WHERE table_schema = 'SALES' AND 
    ((table_name = 'SALES_AND_STORES' AND table_type = 'BASE TABLE') OR 
     (table_name = 'SYS_SALES_AND_STORES' AND table_type = 'VIEW'))
```

На рисунке ниже показано дерево объектов SQL-клиента, которое содержит материализованное представление 
`sales_and_stores`.

<a id="img_mat_view_in_object_tree"></a>
![](materialized_view_in_tree.png){:height="70%" width="70%"}
{: .figure-center}
*Материализованное представление в дереве объектов*
{: .figure-caption-center}

## Проверка наличия внешней таблицы

Чтобы проверить наличие внешней таблицы загрузки или выгрузки, проверьте дерево объектов в вашем SQL-клиенте 
(см. рисунки [ниже](#img_upload_table_in_tree)). Если внешняя таблица существует, она присутствует среди 
объектов логической БД в SQL-клиенте, иначе — отсутствует среди объектов логической БД.

На рисунках ниже показаны фрагменты дерева объектов SQL-клиента: с внешней таблицей загрузки `sales_ext_upload` 
и внешней таблицей выгрузки `sales_ext_download` соответственно.

<a id="img_upload_table_in_tree"></a>
![](upload_table_in_tree.png){:height="50%" width="50%"}
{: .figure-center}
*Внешняя таблица загрузки в дереве объектов*
{: .figure-caption-center}

<a id="img_download_table_in_tree"></a>
![](download_table_in_tree.png){:height="50%" width="50%"}
{: .figure-center}
*Внешняя таблица выгрузки в дереве объектов*
{: .figure-caption-center}