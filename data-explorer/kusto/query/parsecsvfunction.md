---
title: parse_csv () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается parse_csv () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6645fd0dcc7062a4afb60fb34ade1c519af27294
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663672"
---
# <a name="parse_csv"></a>parse_csv()

Разделяет заданную строку, представляющую одну запись разделенных значений запятой, и возвращает массив строки с этими значениями.

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

**Синтаксис**

`parse_csv(`*Источник*`)`

**Аргументы**

* *источник*: Строка источника, представляющая одну запись разделенных значений запятой.

**Возвращает**

Массив строки, содержащий значения разделения.

**Примечания**

Встроенные каналы линии, запятые и кавычки могут быть избежаны с помощью двойной отметки котировки (''). Эта функция не поддерживает несколько записей в строку (взята только первая запись).

**Примеры**

```kusto
print result=parse_csv('aa,"b,b,b",cc,"Escaping quotes: ""Title""","line1\nline2"')
```

|набор по|
|---|
|[<br>  "аа",<br>  "b,b,b",<br>  "cc",<br>  "Побег \"цитаты:\"Название ",<br>  "line1'nline2"<br>]|

Полезная нагрузка CSV с несколькими записями:

```kusto
print result_multi_record=parse_csv('record1,a,b,c\nrecord2,x,y,z')
```

|result_multi_record|
|---|
|[<br>  "запись1",<br>  "а",<br>  "b",<br>  "c"<br>]|