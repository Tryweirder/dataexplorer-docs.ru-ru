---
title: Оператор Top-hitters в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается оператор TOP-hitters в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d95c981f999d0842a266702ad5fc733281d45a7d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245798"
---
# <a name="top-hitters-operator"></a>Оператор top-hitters

Возвращает приближение первых *N* результатов (предполагая равномерное распределение входных данных).

```kusto
T | top-hitters 25 of Page by Views 
```

> [!NOTE]
> `top-hitters` — это алгоритм приближения, который должен использоваться при работе с большими данными. Приближение значения Top-hitters основано на алгоритме [Count-min-эскиза](https://en.wikipedia.org/wiki/Count%E2%80%93min_sketch) .  

## <a name="syntax"></a>Синтаксис

*T* `| top-hitters` *NumberOfRows* `of` *sort_key* `[` `by` *Выражение* sort_key T NumberOfRows`]`

## <a name="arguments"></a>Аргументы

* *NumberOfRows*: число возвращаемых строк *T* . Можно указать любое числовое выражение.
* *sort_key*: имя столбца, по которому сортируются строки.
* *выражение*: (необязательно) выражение, которое будет использоваться для оценки Top-hitters. 
    * *выражение*: Top-hitters вернет строки *NumberOfRows* , которые имеют приблизительное максимальное значение Sum (*выражение*). Выражение может быть столбцом или любым другим выражением, результатом вычисления которого является число. 
    *  Если *выражение* не упоминается, то алгоритм Top-hitters будет подсчитывать количество вхождений *ключа сортировки*.  

## <a name="examples"></a>Примеры

### <a name="get-most-frequent-items"></a>Получение наиболее частых элементов 

В следующем примере показано, как найти топ-5 языков с большинством страниц в Википедии (доступ к которому осуществляется после апреля 2016). 

```kusto
PageViews
| where Timestamp > datetime(2016-04-01) and Timestamp < datetime(2016-05-01) 
| top-hitters 5 of Language 
```

|Язык|approximate_count_Language|
|---|---|
|en|1539954127|
|zh|339827659|
|de|262197491|
|ru|227003107|
|fr|207943448|

### <a name="get-top-hitters-based-on-column-value"></a>Получение Top hitters на основе значения столбца

В следующем примере показано, как найти наиболее просмотренные англоязычные страницы Википедии из 2016 года. Запрос использует представления (целочисленное число) для вычисления популярности страницы (число представлений). 

```kusto
PageViews
| where Timestamp > datetime(2016-01-01)
| where Language == "en"
| where Page !has 'Special'
| top-hitters 10 of Page by Views
```

|Страница|approximate_sum_Views|
|---|---|
|Main_Page|1325856754|
|Web_scraping|43979153|
|Java_ (programming_language)|16489491|
|United_States|13928841|
|Википедии|13584915|
|Donald_Trump|12376448|
|YouTube|11917252|
|The_Revenant_ (2015_film)|10714263|
|Star_Wars: _The_Force_Awakens|9770653|
|Портал: Current_events|9578000|
