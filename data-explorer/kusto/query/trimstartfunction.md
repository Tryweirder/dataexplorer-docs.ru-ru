---
title: trim_start () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается trim_start () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1d4ae71f73e76005f89766d974192c8eb24cd74d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505582"
---
# <a name="trim_start"></a>trim_start()

Удаляет ведущее соответствие указанного регулярного выражения.

**Синтаксис**

`trim_start(`*regex* `,` *текст*`)`

**Аргументы**

* *regex*: Строка или [регулярное выражение,](re2.md) чтобы быть обрезаны с начала *текста*.  
* *текст*: Строка.

**Возвращает**

*текст* после обрезки матч *regex* найден в начале *текста*.

**Пример**

Заявление ниже отделки *подстроки* с начала *string_to_trim:*

```kusto
let string_to_trim = @"https://bing.com";
let substring = "https://";
print string_to_trim = string_to_trim,trimmed_string = trim_start(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|https://bing.com|bing.com|

Следующее утверждение обрезает все символы, не связанные со словом, с начала строки:

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_start(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|- Te st1// $|Te st1// $|
|- Te st2// $|Te st2// $|
|- Te st3// $|Te st3// $|
|- Te st4// $|Te st4// $|
|- Te st5// $|Te st5// $|

 