---
title: trim_end () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается trim_end () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6a764752f126408ceb48f1c4a1af5c74014b6eab
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251982"
---
# <a name="trim_end"></a>trim_end()

Удаляет конечную совпадающую часть указанного регулярного выражения.

## <a name="syntax"></a>Синтаксис

`trim_end(`*регулярное выражение* `,` *текстовая надпись*`)`

## <a name="arguments"></a>Аргументы

* *Regex*: строка или [регулярное выражение](re2.md) для усечения из конца *текста*.  
* *Text*: строка.

## <a name="returns"></a>Результаты

*текст* после усечения совпадений *регулярного выражения* , найденного в конце *текста*.

## <a name="example"></a>Пример

Оператор ниже обрезает *подстроку*  с конца *string_to_trim*:

```kusto
let string_to_trim = @"bing.com";
let substring = ".com";
print string_to_trim = string_to_trim,trimmed_string = trim_end(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|--------------|--------------|
|bing.com      |bing          |

Оператор Next обрезает все символы, не являющиеся словами, из конца строки:

```kusto
print str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_end(@"[^\w]+",str)
```

|str          |trimmed_str|
|-------------|-----------|
|-TE ST1//$|-TE ST1  |
|-TE ST2//$|-TE ST2  |
|-TE ST3//$|-TE ST3  |
|-TE ST4//$|-TE ST4  |
|-TE ST5//$|-TE ST5  |