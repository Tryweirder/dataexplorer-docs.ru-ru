---
title: trim_end () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны trim_end() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a6f6ffc264cb436fc61d74f08dfded915caa05d4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505650"
---
# <a name="trim_end"></a>trim_end()

Удаляет задняя совпадение указанного регулярного выражения.

**Синтаксис**

`trim_end(`*regex* `,` *текст*`)`

**Аргументы**

* *regex*: Строка или [регулярное выражение,](re2.md) чтобы быть обрезаны с конца *текста*.  
* *текст*: Строка.

**Возвращает**

*текст* после обрезки матчей *regex* найден в конце *текста*.

**Пример**

Заявление ниже отделки *подстроки* от конца *string_to_trim:*

```kusto
let string_to_trim = @"bing.com";
let substring = ".com";
print string_to_trim = string_to_trim,trimmed_string = trim_end(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|--------------|--------------|
|bing.com      |bing          |

Следующее утверждение обрезает все символы, не связанные со словом, из конца строки:

```kusto
print str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_end(@"[^\w]+",str)
```

|str          |trimmed_str|
|-------------|-----------|
|- Te st1// $|- Te st1  |
|- Te st2// $|- Te st2  |
|- Te st3// $|- Te st3  |
|- Te st4// $|- Te st4  |
|- Te st5// $|- Te st5  |