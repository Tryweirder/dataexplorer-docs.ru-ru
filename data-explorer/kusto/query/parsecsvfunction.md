---
title: parse_csv () — обозреватель данных Azure
description: В этой статье описывается parse_csv () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1b0f1a0279dcb35e4e196b0f2170f4a64d58eca9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246123"
---
# <a name="parse_csv"></a>parse_csv()

Разделяет заданную строку, представляющую одну запись значений с разделителями-запятыми, и возвращает строковый массив с этими значениями.

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

## <a name="syntax"></a>Синтаксис

`parse_csv(`*источника*`)`

## <a name="arguments"></a>Аргументы

* *Source*: исходная строка, представляющая одну запись значений с разделителями-запятыми.

## <a name="returns"></a>Результаты

Массив строк, содержащий разделенные значения.

**Примечания**

Встроенные строки, запятые и кавычки могут быть экранированы с помощью двойных кавычек (""). Эта функция не поддерживает несколько записей в строке (используется только первая запись).

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result=parse_csv('aa,"b,b,b",cc,"Escaping quotes: ""Title""","line1\nline2"')
```

|набор по|
|---|
|[<br>  "AA",<br>  "b, b, b",<br>  "CC",<br>  "Экранированные кавычки: \" title \" ",<br>  "line1\nline2"<br>]|

Полезные данные CSV с несколькими записями:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result_multi_record=parse_csv('record1,a,b,c\nrecord2,x,y,z')
```

|result_multi_record|
|---|
|[<br>  "record1",<br>  "a",<br>  "b",<br>  "c"<br>]|
