---
title: Краткий справочник по KQL
description: Список полезных функций КЗЛ и их определения с примерами синтаксиса.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: ff9b78af54141f2c7fdbbf7039aad59dca2312a0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500714"
---
# <a name="kql-quick-reference"></a>Краткий справочник по KQL

В этой статье показан список функций и их описания, которые помогут вам начать использовать язык квини Kusto.

| Оператор/Функция                               | Описание                           | Синтаксис                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Фильтр/Поиск/Состояние**                      |**_Найти соответствующие данные путем фильтрации или поиска_** |                      |
| [Где](kusto/query/whereoperator.md)                      | Фильтры на определенном предикате           | `T | where Predicate`                         |
| [где содержит/имеет](kusto/query/whereoperator.md)        | `Contains`: Ищет любой подстроки матч <br> `Has`: Ищет конкретное слово (лучшая производительность)  | `T | where col1 contains/has "[search term]"`|
| [Поиск](kusto/query/searchoperator.md)                    | Поиск всех столбцов в таблице для значения | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [Принять](kusto/query/takeoperator.md)                        | Возвращает указанное количество записей. Используйте для тестирования запроса<br>**_Примечание:_** `_take`q `_limit`и q являются синонимами. | `T | take NumberOfRows` |
| [Случае](kusto/query/casefunction.md)                        | Добавляет условие, аналогичное if/then/elseif в других системах. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](kusto/query/distinctoperator.md)                | Производит таблицу с отчетливым сочетанием предоставленных столбцов таблицы ввода | `distinct [ColumnName], [ColumnName]` |
| **Дата/время**                                   |**_Операции, которые используют функции даты и времени_**               |                          |
|[Назад](kusto/query/agofunction.md)                           | Возвращает время, смещенное по отношению к времени выполнения запроса. Например, `ago(1h)` это за час до чтения текущих часов. | `ago(a_timespan)` |
| [format_datetime](kusto/query/format-datetimefunction.md)  | Возвращает данные в [различных форматах дат.](kusto/query/format-datetimefunction.md#supported-formats) | `format_datetime(datetime , format)` |
| [Бен](kusto/query/binfunction.md)                          | Раунды всех значений в таймфрейме и группируют их | `bin(value,roundTo)` |
| **Создание/удаление столбцов**                   |**_Добавление или удаление столбцов в таблице_** |                                                    |
| [Печати](kusto/query/printoperator.md)                      | Выводы одной строки с одним или одним из скалярных выражений | `print [ColumnName =] ScalarExpression [',' ...]` |
| [проект](kusto/query/projectoperator.md)                  | Выберите столбцы для включения в указанный порядок | `T | project ColumnName [= Expression] [, ...]` <br> Или <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](kusto/query/projectawayoperator.md)         | Выбирает столбцы, чтобы исключить из вывода | `T | project-away ColumnNameOrPattern [, ...]` |
| [Расширить](kusto/query/extendoperator.md)                    | Создает вычисленная колонка и добавляет его в набор результатов | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Сортировка и агрегированный набор данных**                 |**_Реструктуризация данных путем сортировки или группировки их значимыми способами_**|                  |
| [Сортировки](kusto/query/sortoperator.md)                        | Сортирует ряды таблицы ввода одним или более столбиками в порядке восходящего или убывающего | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [top](kusto/query/topoperator.md)                          | Возвращает первые строки N набора данных при сортировке набора данных с помощью`by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [Суммировать](kusto/query/summarizeoperator.md)              | Группирует строки в `by` соответствии с столбиками группы и вычисляет агрегации по каждой группе | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](kusto/query/countoperator.md)                       | Подсчитывает записи в таблице ввода (например, T)<br>Этот оператор является сокращением для`summarize count() `| `T | count` |
| [Присоединиться к](kusto/query/joinoperator.md)                        | Слияние строк двух таблиц для формирования новой таблицы путем сопоставления значений указанного столбца (ы) из каждой таблицы. Поддерживает полный спектр типов `flouter` `inner`соединений: , `rightanti` `rightantisemi`, `rightouter` `innerunique` `leftanti`, `leftantisemi`, `leftouter` `leftsemi`, ,`rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [Союза](kusto/query/unionoperator.md)                      | Занимает две или более таблиц и возвращает все свои строки | `[T1] | union [T2], [T3], …` |
| [Диапазон](kusto/query/rangeoperator.md)                      | Создает таблицу с арифметической серией значений | `range columnName from start to stop step step` |
| **Форматирование данных**                                 | **_Реструктурировать данные на вывод полезным способом_** | |
| [Поиска](kusto/query/lookupoperator.md)                    | Расширяет столбцы таблицы фактов с значениями, просматриваемыми в таблице измерений | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expand](kusto/query/mvexpandoperator.md)               | Превращает динамические массивы в ряды (расширение многозначения) | `T | mv-expand Column` |
| [Анализа](kusto/query/parseoperator.md)                      | оценивает выражение строки и разбирает его на один или несколько вычисляемых столбцов. Используется для структурирования неструктурированных данных. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](kusto/query/make-seriesoperator.md)          | Создает ряд указанных агрегированных значений вдоль заданной оси | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [Позвольте](kusto/query/letstatement.md)                         | Связывает имя с выражениями, которые могут относиться к его связанной стоимости. Значения могут быть выражениями lambda для создания специальных функций как части запроса. Используйте `let` для создания выражений над таблицами, результаты которых выглядят как новая таблица. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Общие сведения**                                     | **_Разное функционирование и функция_** | |
| [invoke](kusto/query/invokeoperator.md)                    | Запускает функцию на столе, которую она получает в качестве ввода. | `T | invoke function([param1, param2])` |
| [оценить pluginName](kusto/query/evaluateoperator.md)     | Оценивает расширение языка запросов (плагины) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Визуализации**                               | **_Операции, отображающие данные в графическом формате_** | |
| [Визуализации](kusto/query/renderoperator.md) | Рендеринг результатов как графический выход | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
