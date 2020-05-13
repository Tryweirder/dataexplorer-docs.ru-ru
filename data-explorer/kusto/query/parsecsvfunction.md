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
ms.openlocfilehash: f01faae3d9339aa23e7e2bb2b1fdae7a652db360
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271219"
---
# <a name="parse_csv"></a>parse_csv()

Разделяет заданную строку, представляющую одну запись значений, разделенных запятыми, и возвращает массив строк с этими значениями.

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

**Синтаксис**

`parse_csv(`*источника*`)`

**Аргументы**

* *Source*: исходная строка, представляющая одну запись значений, разделенных запятыми.

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
