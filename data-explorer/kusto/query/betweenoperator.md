---
title: Оператор between в Azure Data Explorer
description: В этой статье описан оператор between в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: 8bb2049c7bc7c81092eb137c820f650bf88abc4e
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512984"
---
# <a name="between-operator"></a>Оператор between

проверяет, чтобы входные данные находились внутри указанного диапазона.

```kusto
Table1 | where Num1 between (1 .. 10)
Table1 | where Time between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`between` может выполнять операции с любым числовым выражением, выражением даты и времени или выражением временного интервала.
 
## <a name="syntax"></a>Синтаксис

*T* `|` `where` *expr* `between` `(`*leftRange*` .. `*rightRange*`)`   
 
Если в качестве выражения *expr* указывается значение даты и времени, предоставляется другой синтаксический сахар:

*T* `|` `where` *expr* `between` `(`*leftRangeDateTime*` .. `*rightRangeTimespan*`)`   

## <a name="arguments"></a>Аргументы

* *T* — табличные входные данные, записи которых будут сопоставлены.
* *expr* — выражение для фильтрации.
* *leftRange* —выражение диапазона слева (включительно).
* *rightRange* —выражение диапазона справа (включительно).

## <a name="returns"></a>Возвращаемое значение

Строки в *T*, для которых предикат (*expr* >= *leftRange* и *expr* <= *rightRange*) принимает значение `true`.

## <a name="examples"></a>Примеры  

**Фильтрация числовых значений с помощью оператора between**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 100 step 1
| where x between (50 .. 55)
```

|x|
|---|
|50|
|51|
|52|
|53|
|54|
|55|

**Фильтрация значений даты и времени с помощью оператора between**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Счетчик|
|---|
|476|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count 
```

|Счетчик|
|---|
|476|
