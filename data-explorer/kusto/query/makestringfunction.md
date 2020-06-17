---
title: make_string () — обозреватель данных Azure
description: В этой статье описывается make_string () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 035be5910d173c75e585baa0b093dc6276bd4d63
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818582"
---
# <a name="make_string"></a>make_string()

Возвращает строку, сформированную символами Юникода.
    
**Синтаксис**

`make_string (`*Arg1*[, *ArgN*]...`)`

**Аргументы**

* *Arg1* ... *ArgN*: выражения, являющиеся целыми числами (int или long), или динамическое значение, содержащее массив целых чисел.

* Эта функция получает до 64 аргументов.

**Возвращает**

Возвращает строку, состоящие из символов Юникода, значение codepoint которых указывается в аргументах этой функции. Входные данные должны состоять из допустимого кодовыми точкамиа Юникода.
Если какой-либо аргумент не сопоставлен с символом Юникода, функция возвращает значение `null` .

**Примеры**

```kusto
print str = make_string(75, 117, 115, 116, 111)
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(dynamic([75, 117, 115, 116, 111]))
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(dynamic([75, 117, 115]), 116, 111)
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(75, 10, 117, 10, 115, 10, 116, 10, 111)
```

|str|
|---|
|K<br>u<br>s<br>т<br>o|
