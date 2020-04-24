---
title: отделка () - Azure Data Explorer Документы Майкрософт
description: В этой статье описана отделка () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aef2a61e5ac13fe9af9d8bc0dd130f3d085a3604
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505599"
---
# <a name="trim"></a>trim()

Удаляет все ведущие и задние совпадения указанного регулярного выражения.

**Синтаксис**

`trim(`*regex* `,` *текст*`)`

**Аргументы**

* *regex*: Строка или [регулярное выражение,](re2.md) подготавливанное с самого начала и/или конца *текста.*  
* *текст*: Строка.

**Возвращает**

*текст* после обрезки совпадений *regex* найден в начале и/или в конце *текста.*

**Пример**

Заявление ниже обрезки *подстроки* от начала и в конце *string_to_trim:*

```kusto
let string_to_trim = @"--https://bing.com--";
let substring = "--";
print string_to_trim = string_to_trim, trimmed_string = trim(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|--https://bing.com--|https://bing.com|

Следующее утверждение обрезает все символы, не связанные со словом, от начала и конца строки:

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|- Te st1// $|Te st1|
|- Te st2// $|Te st2|
|- Te st3// $|Te st3|
|- Te st4// $|Te st4|
|- Te st5// $|Te st5|


 