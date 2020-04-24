---
title: make_string () - Исследователь данных Azure Документы Майкрософт
description: Эта статья описывает make_string () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d5af7cab9106088064048c1077ec3f9b1950ec08
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512637"
---
# <a name="make_string"></a>make_string()

Возвращает строку, генерируемую символами Unicode.
    
**Синтаксис**

`make_string (`*Арг1* , *АргН...*`)`

**Аргументы**

* *Arg1* ... *ArgN* : выражения, которые являются целыми числами (int или long) или динамическим значением, держащим массив интегральных чисел.

* Эта функция получает до 64 аргументов. 

**Возвращает**

Возвращает строку, состоящую из символов Unicode, значение кода которого обеспечивается аргументами к этой функции. Ввод должен состоять из действительных кодовых точек Unicode.
Если какой-либо аргумент не отображается на символ Еникожа, функция возвращается в нулевые.

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
|K<br>u<br>s<br>t<br>o|