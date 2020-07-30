---
title: trim_end () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается trim_end () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cab78680a3b996234724bc052d75959928520289
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87339856"
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

Оператор ниже обрезает *подстроку* с конца *string_to_trim*:

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