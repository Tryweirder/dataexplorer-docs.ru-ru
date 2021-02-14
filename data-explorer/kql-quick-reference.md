---
title: Краткий справочник по KQL
description: Список полезных функций KQL и их определений с примерами синтаксиса.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: 2fef26a22388d092cd5b8baa7ebca11ae7da4d05
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359715"
---
# <a name="kql-quick-reference"></a>Краткий справочник по KQL

В этой статье приведен список функций и их описание, которые помогут вам приступить к работе с языком запросов Kusto.

| Оператор или функция                               | Описание                           | Синтаксис                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Filter/Search/Condition**                      |**_Поиск нужных данных путем фильтрации или поиска_** |                      |
| [where](kusto/query/whereoperator.md)                      | Фильтрация по определенному предикату           | `T | where Predicate`                         |
| [where contains/has](kusto/query/whereoperator.md)        | `Contains`. Поиск соответствия подстроки <br> `Has`. Поиск конкретного слова (более высокая производительность)  | `T | where col1 contains/has "[search term]"`|
| [search](kusto/query/searchoperator.md)                    | Выполняет поиск значения во всех столбцах в таблице | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [take](kusto/query/takeoperator.md)                        | Возвращает указанное число записей. Используется для проверки запроса<br>**_Примечание_**. `_take`_ и `_limit`_ являются синонимами. | `T | take NumberOfRows` |
| [case](kusto/query/casefunction.md)                        | Добавляет оператор condition, аналогичный if/then/elseif в других системах. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](kusto/query/distinctoperator.md)                | Создает таблицу с уникальной комбинацией указанных столбцов входной таблицы. | `distinct [ColumnName], [ColumnName]` |
| **Date/Time**                                   |**_Операции, использующие функции даты и времени_**               |                          |
|[ago](kusto/query/agofunction.md)                           | Возвращает смещение времени относительно времени выполнения запроса. Например, `ago(1h)` имеет значение "за один час до считывания текущего часового пояса". | `ago(a_timespan)` |
| [format_datetime](kusto/query/format-datetimefunction.md)  | Возвращает данные в [различных форматах даты](kusto/query/format-datetimefunction.md#supported-formats). | `format_datetime(datetime , format)` |
| [bin](kusto/query/binfunction.md)                          | Округляет все значения во временном интервале и группирует их | `bin(value,roundTo)` |
| **Create/Remove Columns**                   |**_Добавление столбцов в таблицу и их удаление из нее_** |                                                    |
| [print](kusto/query/printoperator.md)                      | Выводит одну строку с одним или несколькими скалярными выражениями | `print [ColumnName =] ScalarExpression [',' ...]` |
| [project](kusto/query/projectoperator.md)                  | Выбирает столбцы для включения в указанном порядке | `T | project ColumnName [= Expression] [, ...]` <br> либо <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](kusto/query/projectawayoperator.md)         | Выбирает столбцы, которые нужно исключить из выходных данных | `T | project-away ColumnNameOrPattern [, ...]` |
| [project-keep](kusto/query/project-keep-operator.md)         | Выбирает столбцы, которые нужно отображать в выходных данных | `T | project-keep ColumnNameOrPattern [, ...]` |
| [project-rename](kusto/query/projectrenameoperator.md)     | Переименовывает столбцы в выходных данных результата | `T | project-rename new_column_name = column_name` |
| [project-reorder](kusto/query/projectreorderoperator.md)   | Переупорядочивает столбцы в выходных данных результата | `T | project-reorder Col2, Col1, Col* asc` |
| [extend](kusto/query/extendoperator.md)                    | Создает вычисляемый столбец и добавляет его в результирующий набор | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Sort and Aggregate Dataset**                 |**_Реструктуризация данных путем их результативной сортировки или группировки_**|                  |
| [sort](kusto/query/sortoperator.md)                        | Сортирует строки таблицы входных данных по одному или нескольким столбцам по возрастанию или убыванию | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [В начало](kusto/query/topoperator.md)                          | Возвращает первые N строк набора данных при сортировке набора данных с помощью `by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [summarize](kusto/query/summarizeoperator.md)              | Группирует строки в соответствии со столбцами группы `by` и вычисляет агрегаты для каждой группы | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](kusto/query/countoperator.md)                       | Подсчитывает количество записей в таблице входных данных (например, T)<br>Этот оператор является сокращением от `summarize count() `| `T | count` |
| [join](kusto/query/joinoperator.md)                        | Объединяет строки двух таблиц для формирования новой таблицы путем сопоставления значений указанных столбцов каждой таблицы. Поддерживает полный диапазон типов объединения: `flouter`, `inner`, `innerunique`, `leftanti`, `leftantisemi`, `leftouter`, `leftsemi`, `rightanti`, `rightantisemi`, `rightouter`, `rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [union](kusto/query/unionoperator.md)                      | Принимает несколько таблиц и возвращает все их строки | `[T1] | union [T2], [T3], …` |
| [range](kusto/query/rangeoperator.md)                      | Формирует таблицу с арифметическими рядами значений | `range columnName from start to stop step step` |
| **Format Data**                                 | **_Преобразование данных для вывода в удобном виде_** | |
| [lookup](kusto/query/lookupoperator.md)                    | Расширяет столбцы таблицы фактов значениями, найденными в таблице измерения | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expand](kusto/query/mvexpandoperator.md)               | Преобразует динамические массивы в строки (расширение для нескольких значений) | `T | mv-expand Column` |
| [parse](kusto/query/parseoperator.md)                      | оценивает выражение строки и разбирает его на один или несколько вычисляемых столбцов. Используется для создания структуры для неструктурированных данных. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](kusto/query/make-seriesoperator.md)          | Создает серию указанных агрегированных значений по заданной оси | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [let](kusto/query/letstatement.md)                         | Привязывает имя к выражениям, которые могут ссылаться на его связанное значение. Значения могут быть лямбда-выражениями для создания специальных функций в составе запроса. Используйте `let` для создания выражений по таблицам, результаты которых выглядят как новая таблица. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Общие**                                     | **_Прочие операции и функции_** | |
| [invoke](kusto/query/invokeoperator.md)                    | Выполняет функцию для таблицы, которая получает результат этой функции в качестве входных данных. | `T | invoke function([param1, param2])` |
| [evaluate pluginName](kusto/query/evaluateoperator.md)     | Оценивает расширения языка запросов (подключаемые модули) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Визуализация**                               | **_Операции, отображающие данные в графическом формате_** | |
| [render](kusto/query/renderoperator.md) | Выводит результаты в графическом формате | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
