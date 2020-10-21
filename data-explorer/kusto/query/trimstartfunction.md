---
title: trim_start () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается trim_start () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6f4341e984504c89bfc4d5a1265c5193ac6d0297
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251879"
---
# <a name="trim_start"></a>trim_start()

Удаляет начальное соответствие указанного регулярного выражения.

## <a name="syntax"></a>Синтаксис

`trim_start(`*регулярное выражение* `,` *текстовая надпись*`)`

## <a name="arguments"></a>Аргументы

* *Regex*: строковое или [регулярное выражение](re2.md) , которое должно быть обрезано от начала *текста*.  
* *Text*: строка.

## <a name="returns"></a>Результаты

*текст* после усечения совпадений *регулярного выражения* , найденного в начале *текста*.

## <a name="example"></a>Пример

Инструкция ниже обрезает *подстроку*  с начала *string_to_trim*:

```kusto
let string_to_trim = @"https://bing.com";
let substring = "https://";
print string_to_trim = string_to_trim,trimmed_string = trim_start(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|https://bing.com|bing.com|

Оператор Next обрезает все символы, не являющиеся словами, из начала строки:

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_start(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|-TE ST1//$|TE ST1//$|
|-TE ST2//$|TE ST2//$|
|-TE ST3//$|TE ST3//$|
|-TE ST4//$|TE ST4//$|
|-TE ST5//$|TE ST5//$|

 