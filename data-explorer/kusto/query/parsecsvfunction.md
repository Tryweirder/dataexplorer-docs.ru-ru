---
title: parse_csv () — обозреватель данных Azure
description: В этой статье описывается parse_csv () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b61934ec2efbfb22c17fe93a4f3969a1592cefab
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780666"
---
# <a name="parse_csv"></a>parse_csv()

Разделяет заданную строку, представляющую одну запись значений с разделителями-запятыми, и возвращает строковый массив с этими значениями.

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

**Синтаксис**

`parse_csv(`*источника*`)`

**Аргументы**

* *Source*: исходная строка, представляющая одну запись значений с разделителями-запятыми.

**Возвращает**

Массив строк, содержащий разделенные значения.

**Примечания**

Встроенные строки, запятые и кавычки могут быть экранированы с помощью двойных кавычек (""). Эта функция не поддерживает несколько записей в строке (используется только первая запись).

**Примеры**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result=parse_csv('aa,"b,b,b",cc,"Escaping quotes: ""Title""","line1\nline2"')
```

|result|
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
