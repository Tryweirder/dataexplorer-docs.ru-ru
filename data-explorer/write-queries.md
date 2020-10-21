---
title: Написание запросов для обозревателя данных Azure
description: В этом практическом руководстве вы узнаете, как выполнять базовые и более сложные запросы для обозревателя данных Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: tutorial
ms.date: 04/07/2019
ms.openlocfilehash: f6d993383ecf6c25bf144228e77f02f944bc02d8
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343340"
---
# <a name="write-queries-for-azure-data-explorer"></a>Написание запросов для обозревателя данных Azure

В этой статье вы узнаете, как использовать язык запросов в обозревателе данных Azure для выполнения простых запросов с наиболее распространенными операторами. Вы также познакомитесь с некоторыми более сложными функциями языка.

## <a name="prerequisites"></a>Предварительные условия

Запросы в этой статье можно выполнять одним из двух способов:

- На *вспомогательном кластере* в обозревателе данных Azure, который мы настроили для использования в учебных целях.
    [Войдите в кластер](https://dataexplorer.azure.com/clusters/help/databases/samples) с учетной записью электронной почты организации, которая входит в Azure Active Directory.

- На вашем собственном кластере, который включает примеры данных StormEvents. Дополнительные сведения см. в разделах [Краткое руководство. Создание кластера и базы данных обозревателя данных Azure](create-cluster-database-portal.md) и [Передача данных примера в обозреватель данных Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>Общие сведения о языке запросов

В Azure Data Explorer запрос позволяет обработать доступные только для чтения данные и получить результаты обработки. Запрос указывается в виде обычного текста с использованием модели потока данных, предназначенной для того, чтобы сделать синтаксис простым для чтения, написания и автоматизации запросов. В запросе используются сущности схемы, упорядоченные в иерархию, аналогичную SQL: базы данных, таблицы и столбцы.

Запрос состоит из последовательности операторов запроса, разделенных точкой с запятой (`;`). Хотя бы один оператор должен представлять собой оператор табличного выражения, это оператор, который формирует данные в виде табличной сетки из строк и столбцов. Операторы табличных выражений запроса формируют результаты запроса.

Синтаксис оператора табличного выражения содержит табличный поток данных из одного оператора табличного запроса в другой, начиная с источника данных (например, таблицы в базе данных или оператора, формирующего данные) и проходя через набор операторов преобразования данных, которые объединены вместе с помощью разделителей канала (`|`).

Например, следующий запрос содержит один оператор, который является оператором табличного выражения. Оператор начинается со ссылки на таблицу с именем `StormEvents` (базы данных, в которой размещена эта таблица, указана неявно, и входит в информацию о соединении). Данные (строки) для этой таблицы затем фильтруются по значению столбца `StartTime` и затем фильтруются по значению столбца `State`. Запрос возвращает количество "выживших" строк.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA) **\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

В данном случае результат таков:

|Count|
|-----|
|   23|
| |

Дополнительные сведения см. в [справочнике по языку запросов](./kusto/query/index.md).

## <a name="most-common-operators"></a>Наиболее распространенные операторы

Операторы, описанные в этом разделе, являются стандартными блоками, необходимыми для понимания запросов в обозревателе данных Azure. Большинство запросов, которые вы пишете, будут включать некоторые из этих операторов.

Для выполнения запросов в кластере: выберите **Щелкните, чтобы выполнить запрос** над каждым запросом.

Для выполнения запросов в собственном кластере:

1. Скопируйте каждый запрос в веб-приложение запроса и выберите запрос или поместите курсор в запрос.

1. В верхней части приложения нажмите кнопку **Выполнить**.

### <a name="count"></a>count

[**count**](kusto/query/countoperator.md): возвращает количество строк в таблице.

Следующий запрос возвращает количество строк в таблице StormEvents.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA) **\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>take

[**take**](kusto/query/takeoperator.md): возвращает заданное количество строк данных.

Следующий запрос возвращает пять строк из таблицы StormEvents. Ключевое слово *limit* является псевдонимом *take*.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d) **\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Если источник данных не отсортирован, записи могут возвращаться в любом порядке.

### <a name="project"></a>проект

[**project**](kusto/query/projectoperator.md): выбирает подмножество столбцов.

Следующий запрос возвращает указанное множество столбцов.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA) **\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>где

[**where**](kusto/query/whereoperator.md): Отфильтровывает таблицу до подмножества строк, которые удовлетворяют предикату.

Следующий запрос фильтрует данные по `EventType` и `State`.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>sort

[**sort**](kusto/query/sortoperator.md): сортирует строки входной таблицы по порядку (по одному или нескольким столбцам).

Следующий запрос сортирует данные в убывающем порядке по `DamageProperty`.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> Порядок операций важен. Попробуйте поместить `take 5` перед `sort by`. Результат отличается от предыдущего?

### <a name="top"></a>top

[**top**](kusto/query/topoperator.md): возвращает первые *N* записей, отсортированные по указанным столбцам.

Следующий запрос возвращает те же результаты, что приведенный выше запрос. При этом запрос содержит на один оператор меньше по сравнению с предыдущим.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>extend

[**extend**](kusto/query/extendoperator.md): вычисляет производные столбцы.

Следующий запрос создает новый столбец путем вычисления значения в каждой строке.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

Выражения могут включать в себя все обычные операторы (+, -, *, /, %) и ряд полезных функций, которые можно вызвать.

### <a name="summarize"></a>summarize

[**summarize**](kusto/query/summarizeoperator.md): агрегирует группы строк.

Следующий запрос возвращает количество событий по `State`.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA) **\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

Оператор **summarize** группирует строки, имеющие одинаковые значения, в предложении **by**, а затем использует статистическую функцию (такую как **count**) для объединения каждой группы в одну строку. Таким образом, в этом случае у нас есть по одной строке для каждого штата и столбец с количеством строк в этом штате.

Существует широкий спектр **статистических функций**, и в одном операторе суммирования можно использовать некоторые из них для получения нескольких вычисляемых столбцов. Например, вы можете получить количество штормов в каждом штате и уникальное количество штормов для каждого штата и затем использовать оператор **top** для определения штатов, в которых штормы происходили чаще всего.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d) **\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Результат операции **summarize**:

- Каждый столбец, указанный в **by**

- Столбец для каждого вычисляемого выражения

- Строка для каждого сочетания значений

### <a name="render"></a>render

[**render**](kusto/query/renderoperator.md): выводит результаты в графическом формате.

Следующий запрос отображает гистограмму.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d) **\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

Следующий запрос отображает простую временную диаграмму.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA) **\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

Следующий запрос подсчитывает события по времени за один день, сгруппированные по часам, и отображает временную диаграмму.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d) **\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

В следующем запросе сравниваются несколько дневных последовательностей на временной диаграмме.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA) **\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> Оператор **render** является клиентской, а не серверной функцией. Он добавлен в язык для удобства использования. Веб-приложение поддерживает следующие варианты диаграмм: гистограмма, круговая диаграмма, временная диаграмма и линейная диаграмма. 

## <a name="scalar-operators"></a>Скалярные операторы

В этом разделе рассматриваются некоторые из наиболее важных скалярных операторов.

### <a name="bin"></a>bin()

[**bin()** ](kusto/query/binfunction.md): Округляет значения до целого, кратного указанному размеру группы.

Следующий запрос вычисляет количество с размером контейнера в течение одного дня.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>case()

[**case()**](kusto/query/casefunction.md): вычисляет список предикатов и возвращает первое выражение, результат которого является удовлетворительным, или последнее выражение **else**. Этот оператор можно использовать для классификации или группирования данных:

Следующий запрос возвращает новый столбец `deaths_bucket` и группирует случаи смерти по количеству.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d) **\]**

```Kusto
StormEvents
| summarize deaths = sum(DeathsDirect) by State
| extend deaths_bucket = case (
    deaths > 50, "large",
    deaths > 10, "medium",
    deaths > 0, "small",
    "N/A")
| sort by State asc
```

### <a name="extract"></a>extract()

[**extract()**](kusto/query/extractfunction.md): определяет соответствие регулярного выражения из текстовой строки.

Следующий запрос извлекает указанные значения атрибутов из трассировки.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d) **\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

В этом запросе используется оператор **let**, который связывает имя (в данном случае `MyData`) с выражением. До конца области, в которой появляется оператор **let** (глобальная область или область тела функции), имя может использоваться для ссылки на его связанное значение.

### <a name="parse_json"></a>parse_json()

[**parse_json()**](kusto/query/parsejsonfunction.md): интерпретирует строку как значение JSON и возвращает значение как динамическое. Этот метод предпочтительнее чем функция **extractjson()**, если необходимо извлечь более одного элемента из составного объекта JSON.

Следующий запрос извлекает элементы JSON из массива.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d) **\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

Следующий запрос извлекает элементы JSON.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA) **\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

Следующий запрос извлекает элементы JSON с типом динамических данных.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA) **\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>ago()

[**ago()** ](kusto/query/agofunction.md): Вычитает заданный интервал времени из текущего времени UTC.

Следующий запрос возвращает данные за последние 12 часов.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA) **\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mv-expand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>startofweek()

[**startofweek()**](kusto/query/startofweekfunction.md): возвращает дату начала недели, содержащей указанную дату. При указании смещения возвращается дата с указанным смещением.

Следующий запрос возвращает дату начала недели с различными смещениями.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d) **\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

В этом запросе используется оператор **range**, который создает таблицу с одним столбцом значений. См. также: [**startofday()**](kusto/query/startofdayfunction.md), [**startofweek()**](kusto/query/startofweekfunction.md), [**startofyear()**](kusto/query/startofyearfunction.md)), [**startofmonth()**](kusto/query/startofmonthfunction.md), [**endofday()**](kusto/query/endofdayfunction.md), [**endofweek()**](kusto/query/endofweekfunction.md), [**endofmonth()**](kusto/query/endofmonthfunction.md) и [**endofyear()**](kusto/query/endofyearfunction.md).

### <a name="between"></a>between()

[**between()**](kusto/query/betweenoperator.md): проверяет, что входные данные находятся внутри указанного диапазона.

Следующий запрос фильтрует данные по указанному диапазону дат.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

Следующий запрос фильтрует данные по указанному диапазону дат с небольшой вариацией в три дня (`3d`) от начальной даты.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Табличные операторы

В языке Kusto есть множество табличных операторов, некоторые из них описаны в других разделах этой статьи. Здесь мы сосредоточимся на операторе **parse**. 

### <a name="parse"></a>parse

[**parse**](kusto/query/parseoperator.md): оценивает выражение строки и разбирает его на один или несколько вычисляемых столбцов. Существует три варианта анализа: простой (по умолчанию), с помощью регулярных выражений и облегченный.

Следующий запрос выполняет синтаксический анализ трассировки и извлекает необходимые значения с использованием простого варианта анализа. Выражение (указанное как StringConstant) — это значение строки регулярного выражения, и соответствие является строгим: расширенные столбцы должны соответствовать требуемым типам.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse EventTrace with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

Следующий запрос выполняет синтаксический анализ трассировки и извлекает необходимые значения с использованием `kind = regex`. StringConstant может быть регулярным выражением.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind = regex EventTrace with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

Следующий запрос выполняет синтаксический анализ трассировки и извлекает необходимые значения с использованием `kind = relaxed`. StringConstant — это значение строки регулярного выражения, и соответствие является нестрогим: расширенные столбцы могут частично соответствовать требуемым типам.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind=relaxed "Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=NULL, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=NULL, previousLockTime=02/17/2016 08:39:01)" with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

## <a name="time-series-analysis"></a>Аналитика временных рядов

### <a name="make-series"></a>make-series

[**make-series**](kusto/query/make-seriesoperator.md): объединяет группы строк, как и оператор [summarize](kusto/query/summarizeoperator.md), но создает вектор временных рядов для каждого сочетания значений.

Следующий запрос возвращает набор временных рядов для количества штормов в день. Запрос охватывает трехмесячный период для каждого штата. Для отсутствующих значений указывается константа 0:

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d) **\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

После создания набора временных рядов вы можете использовать ряд функций для определения аномальных фигур, сезонных закономерностей и многого другого.

Следующий запрос извлекает три первых штата, в которых в указанный день число событий было максимальным:

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d) **\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

Дополнительные сведения см. в полном списке [функций для работы с рядами](kusto/query/scalarfunctions.md#series-processing-functions).

## <a name="advanced-aggregations"></a>Расширенные агрегатные функции

Мы рассмотрели основные статистические функции, такие как **count** и **summarize**, описанные ранее в этой статье. В этом разделе представлены более сложные функции.

### <a name="top-nested"></a>top-nested

[**top-nested**](kusto/query/topnestedoperator.md): формирует выборку результатов, состоящую из первых нескольких уровней, где каждый следующий уровень детализирует предыдущие уровни.

Этот оператор может быть удобен в сценариях визуализации панелей мониторинга или для ответов на такие вопросы, как "Найти первые N значений K1 (с использованием некоторой статистической функции); для каждого из них найти первые M значений K2 (с использованием другой статистической функции); ..."

Следующий запрос возвращает иерархическую таблицу с верхним уровнем `State`, за которым следует `Sources`.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>Подключаемый модуль pivot()

[**Подключаемый модуль pivot()**](kusto/query/pivotplugin.md): поворачивает таблицу, превращая уникальные значения из одного столбца во входной таблице в несколько столбцов в выходной таблице. Оператор выполняет агрегирование для оставшихся значений столбца в выходных данных там, где это необходимо.

Следующий запрос применяется фильтр и сводит строки в столбцы.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d) **\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>dcount()

[**dcount()**](kusto/query/dcount-aggfunction.md): возвращает приблизительное количество различных значений выражения для группы. Используйте [**count()**](kusto/query/countoperator.md) для подсчета всех значений.

Следующий запрос подсчитывает количество уникальных `Source` по `State`.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d) **\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()**](kusto/query/dcountif-aggfunction.md): возвращает приблизительное количество различных значений выражения для строк, для которых предикат возвращает значение true.

Следующий запрос подсчитывает количество различных значений `Source`, где `DamageProperty < 5000`.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d) **\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcount_hll"></a>dcount_hll()

[**dcount_hll()**](kusto/query/dcount-hllfunction.md): вычисляет **dcount** для результатов HyperLogLog (созданных [**hll**](kusto/query/hll-aggfunction.md) или [**hll_merge**](kusto/query/hll-merge-aggfunction.md).

Следующий запрос использует алгоритм HLL для получения количества.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA) **\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="arg_max"></a>arg_max()

[**arg_max()**](kusto/query/arg-max-aggfunction.md): находит в группе строку, для которой выражение будет максимальным, и возвращает значение другого выражения (можно указать *, чтобы вернуть всю строку).

Следующий запрос возвращает время последнего сообщения о наводнении в каждом штате.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA) **\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset()**](kusto/query/makeset-aggfunction.md): возвращает динамический массив (JSON) с набором различных значений, которые выражение принимает в группе.

Следующий запрос возвращает все значения времени, в которые поступали сообщения о наводнении, для каждого штата, и создает массив из набора уникальных значений.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA) **\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mv-expand"></a>mv-expand

[**mv-expand**](kusto/query/mvexpandoperator.md): расширяет коллекции с несколькими значениями из столбца динамического типа, так что каждое значение в коллекции получает отдельную строку. Все остальные столбцы в развернутой строке дублируются. Действие этого оператора противоположно действию оператора makelist.

Следующий запрос формирует пример данных. Для этого в нем создается набор, который затем используется для демонстрации возможностей оператора **mv-expand**.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA) **\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mv-expand FloodReports
```

### <a name="percentiles"></a>percentiles()

[**percentiles()**](kusto/query/percentiles-aggfunction.md): возвращает оценку указанного [**процентиля с ближайшим рангом**](kusto/query/percentiles-aggfunction.md) для популяции, определяемой выражением. Точность зависит от плотности заполнения области процентиля. Может использоваться только в контексте статистической обработки в операторе [**summarize**](kusto/query/summarizeoperator.md).

Следующий запрос вычисляет процентили для продолжительности шторма.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA) **\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

Следующий запрос вычисляет процентили для длительности шторма в каждом штате и нормализует данные по пятиминутным интервалам (`5m`).

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d) **\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Перекрестный набор данных

В этом разделе рассматриваются элементы, которые позволяют создавать более сложные запросы, объединять данные в таблицах и выполнять запросы для баз данных и кластеров.

### <a name="let"></a>let

[**let**](kusto/query/letstatement.md): улучшает модульность и повторное использование. Оператор **let** позволяет разбить потенциально сложное выражение на несколько частей, у каждой из которых есть имя, и объединить эти части вместе. Оператор **let** также можно использовать для создания пользовательских функций и представлений (выражения, оперирующие с таблицами, результаты которых представляют собой новые таблицы). Выражения, связанные с помощью оператора **let**, могут иметь скалярный тип, табличный тип или представлять собой пользовательские функции (лямбда-выражения).

В следующем примере создается переменная табличного типа, которая используется в последующих выражениях.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d) **\]**

```Kusto
let LightningStorms =
StormEvents
| where EventType == "Lightning";
let AvalancheStorms =
StormEvents
| where EventType == "Avalanche";
LightningStorms
| join (AvalancheStorms) on State
| distinct State
```

### <a name="join"></a>join

[**join**](kusto/query/joinoperator.md): объединение строк двух таблиц для формирования новой таблицы путем сопоставления значений указанных столбцов каждой таблицы. Kusto поддерживает весь диапазон типов объединения: **fullouter**, **inner**, **innerunique**, **leftanti**, **leftantisemi**, **leftouter**, **leftsemi**, **rightanti**, **rightantisemi**, **rightouter** и **rightsemi**.

В следующем примере две таблицы соединяются с помощью внутреннего соединения.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==) **\]**

```Kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X 
| join kind=inner Y on Key
```

> [!TIP]
> Используйте операторы **where** и **project** перед оператором объединения, чтобы сократить количество строк и столбцов во входных таблицах. Если одна таблица меньше другой, используйте ее в качестве левой (перенаправленной) части объединенных данных. У объединяемых по соответствию столбцов должно быть одно имя. При необходимости переименуйте столбец в одной из таблиц с помощью оператора **project**.

### <a name="serialize"></a>serialize

[**serialize**](kusto/query/serializeoperator.md): сериализует набор строк, так чтобы можно было использовать функции, которые сериализуют данные, например **row_number()**.

Следующий запрос завершается успешно, поскольку данные сериализованы.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

Набор строк также считается сериализованным, если он является результатом оператора **sort**, **top** или **range**, за которым могут следовать операторы **project**, **project-away**, **extend**, **where**, **parse**, **mv-expand** или **take**.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA) **\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Запросы между базами данных и между кластерами

[Запросы между базами данных и между кластерами](kusto/query/cross-cluster-or-database-queries.md): чтобы отправить запрос к базе данных в том же кластере, нужно указать ее как `database("MyDatabase").MyTable`. Чтобы отправить запрос к базе данных на удаленном кластере, нужно указать ее как `cluster("MyCluster").database("MyDatabase").MyTable`.

Следующий запрос вызывается из одного кластера и запрашивает данные из кластера `MyCluster`. Для выполнения этого запроса используйте имя собственного кластера и имя базы данных.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>Аналитика пользователей

Этот раздел включает элементы и запросы, которые показывают, как легко проанализировать поведение пользователей в Kusto.

### <a name="activity_counts_metrics-plugin"></a>Подключаемый модуль activity_counts_metrics

[**Подключаемый модуль activity_counts_metrics**](kusto/query/activity-counts-metrics-plugin.md): вычисляет полезные метрики действий (общее число значений, число различных значений, число различных новых значений и общее число различных значений). Метрики вычисляются для каждого временного окна, затем они сравниваются и агрегируются со всеми предыдущими временными окнами.

Следующий запрос анализирует степень принятия у пользователей, вычисляя ежедневные счетчики действий.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d) **\]**

```Kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01),
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
T
| evaluate activity_counts_metrics(UserId, Timestamp, start, end,
window)
```

### <a name="activity_engagement-plugin"></a>Подключаемый модуль activity_engagement

[**Подключаемый модуль activity_engagement**](kusto/query/activity-engagement-plugin.md): вычисляет степень вовлеченности действия на основе идентификатора столбца по скользящей временной шкале. **Подключаемый модуль activity_engagement** может использоваться для вычисления показателей DAU, WAU и MAU (активных пользователей за день, неделю и месяц).

Следующий запрос возвращает долю общего количества уникальных пользователей, использующих приложение ежедневно, к общему количеству уникальных пользователей, использующих приложение еженедельно, для скользящего окна длиной в семь дней.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100
| render timechart
```

> [!TIP]
> При вычислении показателей DAU/MAU измените конечную дату и длительность скользящего окна (OuterActivityWindow).

### <a name="activity_metrics-plugin"></a>Подключаемый модуль activity_metrics

[**Подключаемый модуль activity_metrics**](kusto/query/activity-metrics-plugin.md): вычисляет полезные метрики действий (число различных значений, число различных новых значений, частоту удержания и скорость изменения) для окна текущего периода по сравнению с окном предыдущего периода.

Следующий запрос вычисляет скорость изменения и частоту удержания для заданного набора данных.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
| where _day > datetime(2017-01-02)
| project _day, id
// Calculate weekly retention rate
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate*100, churn_rate*100
| render timechart
```

### <a name="new_activity_metrics-plugin"></a>Подключаемый модуль new_activity_metrics

[**Подключаемый модуль new_activity_metrics**](kusto/query/new-activity-metrics-plugin.md): вычисляет полезные метрики действий (число различных значений, число различных новых значений, частоту удержания и скорость изменения) для когорты новых пользователей. Идея этого подключаемого модуля похожа на идею [**подключаемого модуля activity_metrics**](kusto/query/activity-metrics-plugin.md), но он сосредоточен на новых пользователях.

Следующий запрос вычисляет скорость изменения и частоту удержания для недельных окон для когорты новых пользователей (пользователей, которые появились на первой неделе).

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

### <a name="session_count-plugin"></a>Подключаемый модуль session_count

[**Подключаемый модуль session_count**](kusto/query/session-count-plugin.md): вычисляет количество сеансов на основе идентификатора столбца по временной шкале.

Следующий запрос возвращает количество сеансов. Сеанс считается активным, если идентификатор пользователя появляется хотя бы один раз на временном интервале в 100 временных слотов, при том, что окно возврата сеанса составляет 41 временной слот.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d) **\]**

```Kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart
```

### <a name="funnel_sequence-plugin"></a>Подключаемый модуль funnel_sequence

[**Подключаемый модуль funnel_sequence**](kusto/query/funnel-sequence-plugin.md): вычисляет число уникальных пользователей, которые приняли последовательность состояний, отображает распределение предыдущего и следующего состояний, которые привели к последовательности или за которыми она возникла.

В следующем запросе показано, какое событие происходит до и после всех событий торнадо в 2007.

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA) **\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnel_sequence_completion-plugin"></a>Подключаемый модуль funnel_sequence_completion

[**Подключаемый модуль funnel_sequence_completion**](kusto/query/funnel-sequence-completion-plugin.md): вычисляет воронку завершенных шагов последовательности для различных периодов времени.

Следующий запрос проверяет завершение воронки последовательности: `Hail -> Tornado -> Thunderstorm -> Wind` за "общее" время в один час, четыре часа и один день (`[1h, 4h, 1d]`).

**\[** [**Щелкните, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA) **\]**

```Kusto
let _start = datetime(2007-01-01);
let _end = datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm', 'Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods)
```

## <a name="functions"></a>Функции

В этом разделе рассматриваются [**функции**](kusto/query/functions/index.md): многократно используемые запросы, которые хранятся на сервере. Функции могут вызываться запросами и другими функциями (рекурсивные функции не поддерживаются).

> [!NOTE]
> Создавать функции на учебном кластере, который доступен только для чтения, невозможно. Для этого используйте собственный тестовый кластер.

В следующем примере создается функция, которая принимает имя штата (`MyState`) в качестве аргумента.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

В следующем примере вызывается функция, которая получает данные для штата Техас.

```Kusto
MyFunction ("Texas")
| summarize count()
```

В следующем примере удаляется функция, которая была создана на первом шаге.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>Дальнейшие шаги

[Справочник по языку запросов Kusto](./kusto/query/index.md)