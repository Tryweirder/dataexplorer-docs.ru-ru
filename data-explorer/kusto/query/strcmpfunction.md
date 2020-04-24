---
title: strcmp () - Azure Data Explorer Документы Майкрософт
description: В этой статье описана strcmp() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 62ebcbfa71ebf8a29f3a8a1559feb91f96e0a2bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506908"
---
# <a name="strcmp"></a>strcmp()

Сравнивает две строки.

Функция начинает сравнивать первый символ каждой строки. Если они равны друг другу, она продолжается со следующими парами до тех пор, пока символы не отличаются или до конца короче строки достигается.

**Синтаксис**

`strcmp(`*string1* `,` *string2*`)` 

**Аргументы**

* *string1*: первая строка ввода для сравнения. 
* *string2*: вторая строка ввода для сравнения.

**Возвращает**

Возвращает интегральное значение, указывающее на связь между строками:
* *<0* - первый символ, который не соответствует имеет более низкое значение в string1, чем в string2
* *0* - содержимое обеих строк равно
* *>0* - первый символ, который не соответствует имеет большее значение в string1, чем в string2

**Примеры**

```
datatable(string1:string, string2:string)
["ABC","ABC",
"abc","ABC",
"ABC","abc",
"abcde","abc"]
| extend result = strcmp(string1,string2)
```

|строка1|string2|набор по|
|---|---|---|
|ABC|ABC|0|
|abc|ABC|1|
|ABC|abc|-1|
|Abcde|abc|1|