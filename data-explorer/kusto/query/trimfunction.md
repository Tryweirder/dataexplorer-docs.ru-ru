---
title: Trim () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается Trim () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a28ca267612bef68c676118331b3010a8c947e36
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350644"
---
# <a name="trim"></a>trim()

Удаляет все начальные и конечные соответствия указанного регулярного выражения.

## <a name="syntax"></a>Синтаксис

`trim(`*регулярное выражение* `,` *текстовая надпись*`)`

## <a name="arguments"></a>Аргументы

* *Regex*: строковое или [регулярное выражение](re2.md) , которое должно быть обрезано от начала и до конца *текста*.  
* *Text*: строка.

## <a name="returns"></a>Возвращаемое значение

*текст* после усечения совпадений *регулярного выражения* , найденного в начале и (или) конце *текста*.

## <a name="example"></a>Пример

Инструкция ниже обрезает *подстроку* с начала до конца *string_to_trim*:

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


 