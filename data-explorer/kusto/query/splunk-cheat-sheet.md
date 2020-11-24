---
title: Splunk Kusto Map для Azure обозреватель данных и Azure Monitor
description: Сопоставление концепций для пользователей, знакомых с Splunk, чтобы изучить язык запросов Kusto для написания запросов журнала.
ms.service: data-explorer
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 8679b47a2c698c88c4d1773f9c50dee495532ae7
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783239"
---
# <a name="splunk-to-kusto-query-language-map"></a>Схема языка запросов Splunk в Kusto

Эта статья призвана помочь пользователям, знакомым с Splunk, изучить язык запросов Kusto для написания запросов журналов с помощью Kusto. Прямые сравнения между двумя, чтобы выделить ключевые отличия и сходства, можно создать на базе имеющихся знаний.

## <a name="structure-and-concepts"></a>Структура и концепции

В следующей таблице сравниваются основные понятия и структуры данных журналов Splunk и Kusto.

 | Концепция | Splunk | Kusto |  Комментировать |
 |:---|:---|:---|:---|
 | единица развертывания  | cluster |  cluster |  Kusto позволяет выполнять произвольные запросы между кластерами. В Splunk — нет. |
 | кэши данных |  контейнеры  |  политики кэширования и хранения |  Определяет период и уровень кэширования данных. Этот параметр напрямую влияет на производительность запросов и стоимость развертывания. |
 | логическая секция данных  |  index  |  База данных  |  Обеспечивает логическое разделение данных. В обеих реализациях можно объединять и соединять разделы. |
 | метаданные структурированного события | Недоступно | table |  Splunk не предоставляет концепцию метаданных события на языке поиска. Журналы Kusto имеют концепцию таблицы, которая содержит столбцы. Каждый экземпляр события сопоставляется со строкой. |
 | запись данных | event | строка |  Отличается только терминология. |
 | атрибут записи данных | поле |  столбец |  В Kusto этот параметр определен как часть структуры таблицы. В Splunk каждое событие имеет собственный набор полей. |
 | types | тип данных |  тип данных |  Типы данных Kusto более явные, так как они заданы для столбцов. Обе имеют возможность динамически работать с типами данных и приблизительно эквивалентным набором типов, включая поддержку JSON. |
 | запрос и поиск  | search | query |  Понятия, по сути, одинаковы для Kusto и Splunk. |
 | время приема события | системное время | `ingestion_time()` |  В Splunk каждое событие получает системную метку времени, когда событие было проиндексировано. В Kusto можно определить политику с именем [ingestion_time](../management/ingestiontimepolicy.md) , которая предоставляет системный столбец, на который можно ссылаться с помощью функции [ingestion_time ()](ingestiontimefunction.md) . |

## <a name="functions"></a>Функции

В следующей таблице указаны функции в Kusto, эквивалентные функциям Splunk.

|Splunk | Kusto |Комментировать
|:---|:---|:---
| `strcat` | `strcat()` | (1) |
| `split`  | `split()` | (1) |
| `if`     | `iff()`   | (1) |
| `tonumber` | `todouble()`<br />`tolong()`<br />`toint()` | (1) |
| `upper`<br />`lower` |toupper()<br />`tolower()`|(1) |
| `replace` | `replace()` | (1)<br /> Также обратите внимание, что хотя `replace()` принимает три параметра в обоих продуктах, параметры отличаются. |
| `substr` | `substring()` | (1)<br />Обратите внимание, что Splunk использует индексы, отсчитываемые с единицы, Kusto заметок, начинающиеся с нуля. |
| `tolower` |  `tolower()` | (1) |
| `toupper` | `toupper()` | (1) |
| `match` | `matches regex` |  (2)  |
| `regex` | `matches regex` | В Splunk `regex` является оператором. В Kusto это реляционный оператор. |
| `searchmatch` | == | В Splunk `searchmatch` позволяет выполнять поиск конкретных строк.
| `random` | rand()<br />rand(n) | Функция Splunk возвращает число от 0 до 2<sup>31</sup>-1. Kusto возвращает число от 0,0 до 1,0 или, если указан параметр, от 0 до n-1.
| `now` | `now()` | (1)
| `relative_time` | `totimespan()` | (1)<br />В Kusto, эквивалентом Splunk `relative_time(datetimeVal, offsetVal)` является `datetimeVal + totimespan(offsetVal)` .<br />Например, `search` &#124; `eval n=relative_time(now(), "-1d@d")` преобразуется в `...`  &#124; `extend myTime = now() - totimespan("1d")` .

(1) в Splunk функция вызывается с помощью `eval` оператора. В Kusto он используется как часть `extend` или `project` .<br />(2) в Splunk функция вызывается с помощью `eval` оператора. В Kusto его можно использовать с `where` оператором.


## <a name="operators"></a>Операторы

В следующих разделах приведены примеры использования различных операторов в Splunk и Kusto.

> [!NOTE]
> В следующих примерах поле Splunk `rule` сопоставляется с таблицей в Kusto, а отметка времени по умолчанию для Splunk сопоставляется со `ingestion_time()` столбцом Logs Analytics.

### <a name="search"></a>Поиск

В Splunk можно опустить ключевое слово `search` и указать строку без кавычек. В Kusto каждый запрос должен начинаться с `find` , строка, не заключенная в кавычки, является именем столбца, а значение уточняющего запроса должно быть строкой в кавычках. 

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `search` | `search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h` |
| Kusto | `find` | `find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)` |


### <a name="filter"></a>Filter

Запросы журнала Kusto начинаются с табличного результирующего набора, в котором `filter` применяется. В Splunk фильтрация является операцией по умолчанию в текущем индексе. Вы также можете использовать `where` оператор в Splunk, но не рекомендуется.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `search` | `Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h` |
| Kusto | `where` | `Office_Hub_OHubBGTaskError`<br />&#124; `where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)` |

### <a name="get-n-events-or-rows-for-inspection"></a>Получение *n* событий или строк для проверки

Запросы журнала Kusto также поддерживаются в `take` качестве псевдонима для `limit` . В Splunk, если результаты упорядочены, `head` возвращает первые *n* результатов. В Kusto `limit` не упорядочивается, но возвращает первые *n* строк, которые были найдены.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `head` | `Event.Rule=330009.2`<br />&#124; `head 100` |
| Kusto | `limit` | `Office_Hub_OHubBGTaskError`<br />&#124; `limit 100` |

### <a name="get-the-first-n-events-or-rows-ordered-by-a-field-or-column"></a>Получение первых *n* событий или строк, упорядоченных по полю или столбцу

Для наименьших результатов в Splunk используется `tail` . В Kusto можно указать направление упорядочения с помощью `asc` .

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `head` |  `Event.Rule="330009.2"`<br />&#124; `sort Event.Sequence`<br />&#124; `head 20` |
| Kusto | `top` | `Office_Hub_OHubBGTaskError`<br />&#124; `top 20 by Event_Sequence` |

### <a name="extend-the-result-set-with-new-fields-or-columns"></a>Расширение результирующего набора новыми полями или столбцами

Splunk имеет `eval` функцию, но не сравнима с `eval` оператором в Kusto. `eval`Оператор в Splunk и `extend` оператор в Kusto поддерживают только скалярные функции и арифметические операторы.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `eval` |  `Event.Rule=330009.2`<br />&#124; `eval state= if(Data.Exception = "0", "success", "error")` |
| Kusto | `extend` | `Office_Hub_OHubBGTaskError`<br />&#124; `extend state = iif(Data_Exception == 0,"success" ,"error")` |

### <a name="rename"></a>Переименовать

Kusto использует `project-rename` оператор для переименования поля. В `project-rename` операторе запрос может использовать преимущества всех индексов, предварительно созданных для поля. Splunk имеет `rename` оператор, который делает то же самое.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `rename` |  `Event.Rule=330009.2`<br />&#124; `rename Date.Exception as execption` |
| Kusto | `project-rename` | `Office_Hub_OHubBGTaskError`<br />&#124; `project-rename exception = Date_Exception` |

### <a name="format-results-and-projection"></a>Форматирование результатов и проекции

Splunk не имеет оператора, похожего на `project-away` . Для фильтрации полей можно использовать пользовательский интерфейс.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `table` |  `Event.Rule=330009.2`<br />&#124; `table rule, state` |
| Kusto | `project`<br />`project-away` | `Office_Hub_OHubBGTaskError`<br />&#124; `project exception, state` |

### <a name="aggregation"></a>Агрегирование

См. [список доступных функций агрегирования](summarizeoperator.md#list-of-aggregation-functions) .

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `stats` |  `search (Rule=120502.*)`<br />&#124; `stats count by OSEnv, Audience` |
| Kusto | `summarize` | `Office_Hub_OHubBGTaskError`<br />&#124; `summarize count() by App_Platform, Release_Audience` |


### <a name="join"></a>Join

`join` в Splunk есть существенные ограничения. Вложенный запрос имеет ограничение в 10 000 результатов (задается в файле конфигурации развертывания) и доступно ограниченное число разновидностей соединений.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `join` |  `Event.Rule=120103* &#124; stats by Client.Id, Data.Alias` <br />&#124; `join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]` |
| Kusto | `join` | `cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions`<br />&#124; `where  Data_Hresult== -2147221040`<br />&#124; `join kind = inner (Office_System_SystemHealthMetadata`<br />&#124; `summarize by Client_Id, Data_Alias)on Client_Id`   |

### <a name="sort"></a>Сортировка

В Splunk для сортировки в возрастающем порядке необходимо использовать `reverse` оператор. Kusto также поддерживает определение места размещения значений NULL — как в начале, так и в конце.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `sort` |  `Event.Rule=120103`<br />&#124; `sort Data.Hresult` <br />&#124; `reverse` |
| Kusto | `order by` | `Office_Hub_OHubBGTaskError`<br />&#124; `order by Data_Hresult,  desc` |

### <a name="multivalue-expand"></a>Развертывание нескольких значений

Многозначный оператор Expand аналогичен как в Splunk, так и в Kusto.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `mvexpand` |  `mvexpand solutions` |
| Kusto | `mv-expand` | `mv-expand solutions` |

### <a name="result-facets-interesting-fields"></a>Аспекты результата, интересные поля

В разделе Azure Monitor на портале Azure предоставляется только первый столбец. Все столбцы доступны через API.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `fields` |  `Event.Rule=330009.2`<br />&#124; `fields App.Version, App.Platform` |
| Kusto | `facets` | `Office_Excel_BI_PivotTableCreate`<br />&#124; `facet by App_Branch, App_Version` |

### <a name="deduplicate"></a>Дедупликация

В Kusto можно использовать, `summarize arg_min()` чтобы изменить порядок выбора записи.

| Продукт | Оператор | Пример |
|:---|:---|:---|
| Splunk | `dedup` |  `Event.Rule=330009.2`<br />&#124; `dedup device_id sortby -batterylife` |
| Kusto | `summarize arg_max()` | `Office_Excel_BI_PivotTableCreate`<br />&#124; `summarize arg_max(batterylife, *) by device_id` |

## <a name="next-steps"></a>Дальнейшие действия

- Пошаговое руководство по [языку запросов Kusto](tutorial.md?pivots=azuremonitor).
