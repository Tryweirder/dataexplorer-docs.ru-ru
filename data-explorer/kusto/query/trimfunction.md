---
title: Trim () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается Trim () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a5a8bf4884bf6c493c1b3b960fce64fe143ed52e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251891"
---
# <a name="trim"></a>trim()

Удаляет все начальные и конечные соответствия указанного регулярного выражения.

## <a name="syntax"></a>Синтаксис

`trim(`*регулярное выражение* `,` *текстовая надпись*`)`

## <a name="arguments"></a>Аргументы

* *Regex*: строковое или [регулярное выражение](re2.md) , которое должно быть обрезано от начала и до конца *текста*.  
* *Text*: строка.

## <a name="returns"></a>Результаты

*текст* после усечения совпадений *регулярного выражения* , найденного в начале и (или) конце *текста*.

## <a name="example"></a>Пример

Инструкция ниже обрезает *подстроку*  с начала до конца *string_to_trim*:

```kusto
let string_to_trim = @"--https://bing.com--";
let substring = "--";
print string_to_trim = string_to_trim, trimmed_string = trim(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|--https://bing.com--|https://bing.com|

Оператор Next обрезает все символы, не являющиеся словами, из начала и конца строки:

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|-TE ST1//$|TE ST1|
|-TE ST2//$|TE ST2|
|-TE ST3//$|TE ST3|
|-TE ST4//$|TE ST4|
|-TE ST5//$|TE ST5|


 