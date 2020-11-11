---
title: Splunk Kusto языка запросов журнала в Azure Monitor
description: Справка для пользователей, знакомых с Splunk в журналах обучения Kusto.
ms.service: data-explorer
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: f574493f2029d08fd71b44c858592a6fd8467c82
ms.sourcegitcommit: b6f0f112b6ddf402e97c011a902bd70ba408e897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94499113"
---
# <a name="splunk-to-kusto-query-language"></a>Splunk на язык запросов Kusto

Эта статья призвана помочь пользователям, знакомым с Splunk, изучить язык запросов Kusto для написания запросов журналов в Kusto. Прямые сравнения между этими языками выполнены для понимания двух ключевых различий, а также сходств, где можно использовать имеющиеся знания.

## <a name="structure-and-concepts"></a>Структура и концепции

В следующей таблице сравниваются основные понятия и структуры данных журналов Splunk и Kusto.

 | Концепция | Splunk | Kusto |  Комментировать |
 |:---|:---|:---|:---|
 | Единица развертывания  | cluster |  cluster |  Kusto позволяет выполнять произвольные запросы между кластерами. В Splunk — нет. |
 | Кэши данных |  контейнеры  |  Политики кэширования и хранения |  Определяет период и уровень кэширования данных. Этот параметр напрямую влияет на производительность запросов и затраты на развертывание. |
 | Логическое разделение данных  |  индекс  |  База данных  |  Обеспечивает логическое разделение данных. В обеих реализациях можно объединять и соединять разделы. |
 | Структурированные метаданные событий | Недоступно | table |  Splunk не поддерживает концепцию языка поиска в метаданных событий. Журналы Kusto имеют концепцию таблицы, которая содержит столбцы. Каждый экземпляр события сопоставляется со строкой. |
 | Запись данных | event | строка |  Отличается только терминология. |
 | Атрибут записи данных | поле |  столбец |  В Kusto это предопределено как часть структуры таблицы. В Splunk каждое событие имеет собственный набор полей. |
 | Типы | тип данных |  тип данных |  Типы данных Kusto являются более явными, так как они заданы для столбцов. Как в Splunk, так и в Log Analytics можно динамически работать с типами данных и находятся примерно одинаковые наборы типов данных, включая поддержку JSON. |
 | Запрос и поиск  | search | query |  Основные понятия по сути являются одинаковыми между Kusto и Splunk. |
 | Время приема событий | Системное время | ingestion_time() |  В Splunk каждое событие получает метку времени индексирования события. В Kusto можно определить политику с именем ingestion_time, которая предоставляет системный столбец, на который можно ссылаться с помощью функции ingestion_time (). |

## <a name="functions"></a>Функции

В следующей таблице указаны функции в Kusto, эквивалентные функциям Splunk.

|Splunk | Kusto |Комментировать
|:---|:---|:---
| `strcat` | `strcat()` | (1) |
| `split`  | `split()` | (1) |
| `if`     | `iff()`   | (1) |
| `tonumber` | `todouble()`<br>`tolong()`<br>`toint()` | (1) |
| `upper`<br>`lower` |toupper()<br>`tolower()`|(1) |
| `replace` | `replace()` | (1)<br> Кроме того, обратите внимание, что хоть `replace()` и принимает три параметра в обоих продуктах, параметры отличаются. |
| `substr` | `substring()` | (1)<br>Обратите внимание, что Splunk использует индексы, отсчитываемые с единицы, Kusto заметок, начинающиеся с нуля. |
| `tolower` |  `tolower()` | (1) |
| `toupper` | `toupper()` | (1) |
| `match` | `matches regex` |  (2)  |
| `regex` | `matches regex` | В Splunk `regex` является оператором. В Kusto это реляционный оператор. |
| `searchmatch` | == | В Splunk `searchmatch` позволяет выполнять поиск конкретных строк.
| `random` | rand()<br>rand(n) | Функция Splunk возвращает число от 0 до 2<sup>31</sup>-1. Kusto "возвращает число от 0,0 до 1,0 или если предоставленный параметр находится в диапазоне от 0 до n-1.
| `now` | `now()` | (1)
| `relative_time` | `totimespan()` | (1)<br>В Kusto эквивалентом relative_timeа Splunk (Датетимевал, Оффсетвал) является Датетимевал + тотимеспан (Оффсетвал).<br>Например, <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> преобразуется в <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) В Splunk функция вызывается с помощью оператора `eval`. В Kusto он используется как часть `extend` или `project` .<br>(2) В Splunk функция вызывается с помощью оператора `eval`. В Kusto его можно использовать с `where` оператором.


## <a name="operators"></a>Операторы

В следующих разделах приведены примеры использования различных операторов между Splunk и Kusto.

> [!NOTE]
> В следующем примере _правило_ поля Splunk сопоставляется с таблицей в Kusto, а метка времени по умолчанию для Splunk сопоставляется со столбцом Logs _ingestion_time ()_ .

### <a name="search"></a>Поиск
В Splunk можно опустить ключевое слово `search` и указать строку без кавычек. В Kusto каждый запрос должен начинаться с `find` , строка, не заключенная в кавычки, является именем столбца, а значение уточняющего запроса должно быть строкой в кавычках. 

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `search` | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Kusto | `find` | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |


### <a name="filter"></a>Фильтр
Запросы журнала Kusto запускаются из табличного результирующего набора, в котором фильтр. В Splunk фильтрация является операцией по умолчанию в текущем индексе. В Splunk можно также использовать оператор `where`, но не рекомендуется.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `search` | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Kusto | `where` | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |

### <a name="getting-n-eventsrows-for-inspection"></a>Получение n событий или строк для проверки 
Запросы журнала Kusto также поддерживаются в `take` качестве псевдонима для `limit` . В Splunk, если результаты упорядочены, `head` возвращает первые n результатов. В Kusto ограничение не упорядочивается, но возвращает первые n строк, которые были найдены.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `head` | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Kusto | `limit` | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |

### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Получение первых n событий или строк, упорядоченных по полю или столбцу
Для нижних результатов в Splunk используется `tail`. В Kusto можно указать направление упорядочения с помощью `asc` .

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `head` |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Kusto | `top` | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |

### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Расширение результирующего набора с помощью новых полей или столбцов
В Splunk также есть функция `eval`, которую не следует сопоставлять с оператором `eval`. `eval`Оператор в Splunk и `extend` оператор в Kusto поддерживают только скалярные функции и арифметические операторы.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `eval` |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Kusto | `extend` | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |

### <a name="rename"></a>Переименовать 
Kusto использует `project-rename` оператор для переименования поля. `project-rename` позволяет запросу использовать преимущества всех индексов, предварительно созданных для поля. Splunk имеет `rename` оператор для того, чтобы сделать то же самое.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `rename` |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Kusto | `project-rename` | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |

### <a name="format-resultsprojection"></a>Форматирование результатов и проекция
В Splunk нет оператора, аналогичного `project-away`. Для фильтрации полей можно использовать пользовательский интерфейс.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `table` |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Kusto | `project`<br>`project-away` | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |

### <a name="aggregation"></a>Агрегирование
См. [список функций агрегирования](summarizeoperator.md#list-of-aggregation-functions) для различных статистических функций.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `stats` |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Kusto | `summarize` | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |


### <a name="join"></a>Join
Объединение в Splunk имеет значительные ограничения. Вложенный запрос имеет ограничение в 10 000 результатов (задается в файле конфигурации развертывания), а число разновидностей соединения также ограничено.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `join` |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Kusto | `join` | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |

### <a name="sort"></a>Сортировка
В Splunk для сортировки в возрастающем порядке необходимо использовать оператор `reverse`. Kusto также поддерживает определение места размещения значений NULL в начале или в конце.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `sort` |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Kusto | `order by` | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |

### <a name="multivalue-expand"></a>Развертывание нескольких значений
Это аналогичный оператор в Splunk и Kusto.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `mvexpand` |  `mvexpand foo` |
| Kusto | `mvexpand` | `mvexpand foo` |

### <a name="results-facets-interesting-fields"></a>Аспекты результатов, интересующие поля
В разделе Azure Monitor на портале Azure предоставляется только первый столбец. Все столбцы доступны через API.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `fields` |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Kusto | `facets` | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |

### <a name="de-duplicate"></a>Дедупликация
Вместо этого можно использовать `summarize arg_min()`, чтобы изменить порядок, в котором выбираются записи.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `dedup` |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Kusto | `summarize arg_max()` | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |

## <a name="next-steps"></a>Следующие шаги

- Пошаговое руководство по [языку запросов Kusto](tutorial.md?pivots=azuremonitor).
