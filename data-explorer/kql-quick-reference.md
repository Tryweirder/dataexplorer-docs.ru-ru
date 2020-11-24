---
title: Краткий справочник по KQL
description: Список полезных функций ККЛ и их определений с примерами синтаксиса.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.localizationpriority: high
ms.openlocfilehash: 3b007d1688130449c597ef99281ed89b55d880eb
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95511930"
---
# <a name="kql-quick-reference"></a>Краткий справочник по KQL

В этой статье приведен список функций и их описание, которые помогут вам приступить к работе с языком запросов Kusto.

| Оператор или функция                               | Описание                           | Синтаксис                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Фильтр, Поиск и условие**                      |**_Поиск нужных данных путем фильтрации или поиска_** |                      |
| [where](kusto/query/whereoperator.md)                      | Фильтры для определенного предиката           | `T | where Predicate`                         |
| [где Contains/имеет](kusto/query/whereoperator.md)        | `Contains`: Ищет соответствие всех подстрок <br> `Has`: Ищет конкретное слово (более высокую производительность)  | `T | where col1 contains/has "[search term]"`|
| [search](kusto/query/searchoperator.md)                    | Выполняет поиск значения во всех столбцах в таблице. | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [take](kusto/query/takeoperator.md)                        | Возвращает указанное число записей. Использование для проверки запроса<br>**_Примечание_**. `_take` _ и `_limit` _ являются синонимами. | `T | take NumberOfRows` |
| [case](kusto/query/casefunction.md)                        | Добавляет оператор Condition, аналогичный if/then/ElseIf в других системах. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](kusto/query/distinctoperator.md)                | Создает таблицу с уникальной комбинацией указанных столбцов входной таблицы. | `distinct [ColumnName], [ColumnName]` |
| **Дата и время**                                   |**_Операции, использующие функции даты и времени_**               |                          |
|[прошлого](kusto/query/agofunction.md)                           | Возвращает смещение времени относительно времени выполнения запроса. Например, `ago(1h)` это один час до считывания текущего часового пояса. | `ago(a_timespan)` |
| [format_datetime](kusto/query/format-datetimefunction.md)  | Возвращает данные в [различных форматах даты](kusto/query/format-datetimefunction.md#supported-formats). | `format_datetime(datetime , format)` |
| [ячейки](kusto/query/binfunction.md)                          | Округляет все значения в интервале времени и группирует их | `bin(value,roundTo)` |
| **Создание и удаление столбцов**                   |**_Добавление или удаление столбцов в таблице_** |                                                    |
| [print](kusto/query/printoperator.md)                      | Выводит одну строку с одним или несколькими скалярными выражениями | `print [ColumnName =] ScalarExpression [',' ...]` |
| [project](kusto/query/projectoperator.md)                  | Выбирает столбцы для включения в указанный порядок. | `T | project ColumnName [= Expression] [, ...]` <br> Или <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](kusto/query/projectawayoperator.md)         | Выбирает столбцы, исключаемые из выходных данных | `T | project-away ColumnNameOrPattern [, ...]` |
| [проект — Держитесь](kusto/query/project-keep-operator.md)         | Выбирает столбцы для сохранения в выходных данных | `T | project-keep ColumnNameOrPattern [, ...]` |
| [проект-переименование](kusto/query/projectrenameoperator.md)     | Переименовывает столбцы в выходных данных результатов | `T | project-rename new_column_name = column_name` |
| [изменение порядка проектов](kusto/query/projectreorderoperator.md)   | Переупорядочивает столбцы в выходных данных результатов | `T | project-reorder Col2, Col1, Col* asc` |
| [extend](kusto/query/extendoperator.md)                    | Создает вычисляемый столбец и добавляет его в результирующий набор | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Сортировка и агрегирование набора данных**                 |**_Реструктуризация данных путем их сортировки или группировки по понятным путям_**|                  |
| [sort](kusto/query/sortoperator.md)                        | Сортирует строки входной таблицы по одному или нескольким столбцам в порядке возрастания или убывания | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [В начало](kusto/query/topoperator.md)                          | Возвращает первые N строк набора данных при сортировке набора данных с помощью `by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [summarize](kusto/query/summarizeoperator.md)              | Группирует строки в соответствии со `by` столбцами группы и вычисляет агрегаты для каждой группы | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](kusto/query/countoperator.md)                       | Подсчитывает количество записей во входной таблице (например, T)<br>Этот оператор является сокращенным для `summarize count() `| `T | count` |
| [join](kusto/query/joinoperator.md)                        | Объединяет строки двух таблиц для формирования новой таблицы, сопоставляя значения указанных столбцов из каждой таблицы. Поддерживает полный диапазон типов соединений: `flouter` , `inner` ,,, `innerunique` `leftanti` `leftantisemi` , `leftouter` , `leftsemi` , `rightanti` , `rightantisemi` , `rightouter` , `rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [union](kusto/query/unionoperator.md)                      | Принимает две или более таблиц и возвращает все их строки | `[T1] | union [T2], [T3], …` |
| [range](kusto/query/rangeoperator.md)                      | Формирует таблицу с арифметическими последовательностями значений | `range columnName from start to stop step step` |
| **Форматирование данных**                                 | **_Переструктурирование данных для вывода в удобном виде_** | |
| [автоподстановки](kusto/query/lookupoperator.md)                    | Расширяет столбцы таблицы фактов значениями, которые были просмотрены в таблице измерения. | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expand](kusto/query/mvexpandoperator.md)               | Преобразует динамические массивы в строки (расширение для нескольких значений) | `T | mv-expand Column` |
| [проанализировать](kusto/query/parseoperator.md)                      | оценивает выражение строки и разбирает его на один или несколько вычисляемых столбцов. Используется для структурирования неструктурированных данных. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](kusto/query/make-seriesoperator.md)          | Создает ряд указанных агрегированных значений вдоль указанной оси | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [let](kusto/query/letstatement.md)                         | Привязывает имя к выражениям, которые могут ссылаться на связанное ему значение. Значения могут быть лямбда-выражениями для создания специальных функций в составе запроса. Используйте `let` для создания выражений по таблицам, результаты которых выглядят как новая таблица. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Общие**                                     | **_Прочие операции и функции_** | |
| [invoke](kusto/query/invokeoperator.md)                    | Выполняет функцию для таблицы, которую она получает в качестве входных данных. | `T | invoke function([param1, param2])` |
| [Оценка pluginName](kusto/query/evaluateoperator.md)     | Оценивает расширения языка запросов (подключаемые модули) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Визуализация**                               | **_Операции, отображающие данные в графическом формате_** | |
| [обрабатывает](kusto/query/renderoperator.md) | Отображает результаты в виде графического вывода | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
