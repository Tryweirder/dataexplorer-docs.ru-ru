---
title: trim_start () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается trim_start () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4550fb07da37658ecf11a4eb04ecdf199d8ba989
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87339550"
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

Инструкция ниже обрезает *подстроку* с начала *string_to_trim*:

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

 