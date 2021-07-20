﻿---
layout: default
title: SELECT FROM INFORMATION_SCHEMA
nav_order: 30
parent: Запросы SQL+
grand_parent: Справочная информация
has_children: false
has_toc: false
---

# SELECT FROM INFORMATION_SCHEMA

Запрос позволяет получить метаданные объектов [логической схемы](../../../overview/main_concepts/logical_schema/logical_schema.md), 
описанные в разделе [Системные представления (INFORMATION_SCHEMA)](../../system_views/system_views.md). 
Возможности запроса отличаются от возможностей [SELECT](../SELECT/SELECT.md)-запроса к логическим базам данных.

В ответе возвращается:
*   объект ResultSet c выбранными записями при успешном выполнении запроса;
*   исключение при неуспешном выполнении запроса.

## Синтаксис

```sql
SELECT column_list FROM [INFORMATION_SCHEMA.]system_view_name [AS alias_name]
```

Описание параметров запроса см. [ниже](#параметры).

Префикс `INFORMATION_SCHEMA` перед именем системного представления опционален, если до этого был выполнен 
запрос [USE INFORMATION_SCHEMA](../USE_INFORMATION_SCHEMA/USE_INFORMATION_SCHEMA.md). Для имен системных 
представлений и столбцов можно использовать псевдонимы.

В запросе поддерживаются следующие секции, которые должны быть указаны в порядке их перечисления:
*   `JOIN ON` — для соединения данных нескольких системных представлений;
*   `WHERE` — для указания условий выбора данных;
*   `GROUP BY` — для группировки данных;
*   `ORDER BY` — для сортировки данных;
*   `LIMIT` — для ограничения количества возвращаемых строк.

**Внимание:** строковые значения столбцов в секции `WHERE` необходимо указывать в верхнем регистре 
(например, `WHERE table_schema = 'SALES'`).

Поддерживаются следующие типы соединений системных представлений:
*   `[INNER]` — внутреннее соединение,
*   `LEFT [OUTER]` — левое внешнее соединение,
*   `RIGHT [OUTER]` — правое внешнее соединение,
*   `FULL [OUTER]` — полное внешнее соединение,
*   `CROSS` — декартово произведение, ключи соединения не указываются.

## Параметры

*   `column_list` — список выбираемых столбцов. Допустимо указывать символ `*` для выбора всех столбцов;
*   `system_view_name` — имя системного представления, из которого запрашивается информация. Возможные 
    значения см. в разделе [Системные представления (INFORMATION_SCHEMA)](../../system_views/system_views.md);
*   `alias_name` — псевдоним системного представления.

## Ограничения

Не допускается комбинирование подзапросов к `INFORMATION_SCHEMA` с подзапросами к логическим базам данных.

## Примеры

Запрос списка всех [логических БД](../../../overview/main_concepts/logical_db/logical_db.md) 
[окружения](../../../overview/main_concepts/environment/environment.md) 
с лексической сортировкой по возрастанию:
```sql
SELECT schema_name FROM INFORMATION_SCHEMA.schemata
ORDER BY schema_name
```

Запрос информации о логических сущностях логической БД `SALES`:
```sql
SELECT * FROM INFORMATION_SCHEMA.tables
WHERE table_schema = 'SALES'
```

Запрос списка имен, типов и столбцов логических сущностей окружения:
```sql
SELECT TC.table_schema, TC.table_name, TT.table_type, TC.column_name
FROM information_schema.columns AS TC
JOIN information_schema.tables AS TT
  ON TC.table_schema = TT.table_schema and TC.table_name = TT.table_name
ORDER BY TC.table_schema, TC.table_name
```