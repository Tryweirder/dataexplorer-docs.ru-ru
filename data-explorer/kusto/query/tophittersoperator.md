---
title: оператор топ-хиттеров - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны операторы топ-хиттеров в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7105bd4f9818deab1f0fa5dbe25dbb2d5b1b4afc
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663111"
---
# <a name="top-hitters-operator"></a>Оператор top-hitters

Возвращает приближение первых результатов *N* (при условии искажения ввода).

```kusto
T | top-hitters 25 of Page by Views 
```

**Синтаксис**

*T* `| top-hitters` *NumberOfRows* `of` *sort_key* `[` `by` *выражение*`]`

**Аргументы**

* *NumberOfRows*: Количество строк *T,* чтобы вернуться. Можно указать любое численное выражение.
* *sort_key*: Название столбца, с помощью которого можно сортировать строки.
* *выражение*: (необязательно) Выражение, которое будет использоваться для оценки топ-нападающих. 
    * *выражение*: топ-хиттеры вернут *строки NumberOfRows,* которые имеют приближенную максимальную сумму *(выражение).* Выражение может быть столбец, или любое другое выражение, которое оценивает на число. 
    *  Если *выражение* не упоминается, алгоритм топ-хиттеров будет подсчитывать случаи *сортировочного ключа.*  

**Примечания**

`top-hitters`является алгоритмом приближения и должен использоваться при запуске с большими данными. Приближение топ-нападающих основано на алгоритме [Count-Min-Sketch.](https://en.wikipedia.org/wiki/Count%E2%80%93min_sketch)  

**Пример**

## <a name="getting-top-hitters-most-frequent-items"></a>Получение лучших нападающих (наиболее частые предметы) 

Следующий пример показывает, как найти топ-5 языков с большинством страниц в Википедии (доступен после апреля 2016 года). 

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

## <a name="getting-top-hitters-based-on-column-value-"></a>Получение верхних нападающих (на основе значения столбца)

Следующий пример показывает, как найти наиболее просматриваемые английские страницы Википедии 2016 года. Запрос использует "Виды" (целый номер) для расчета популярности страницы (количество просмотров). 

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
|Star_Wars:_The_Force_Awakens|9770653|
|Портал:Current_events|9578000|