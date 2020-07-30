---
title: strcmp () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается strcmp () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 11951195d95d956f70d4bfce32d22a9f9c73dc3d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350933"
---
# <a name="strcmp"></a>strcmp()

Сравнивает две строки.

Функция начинает сравнивать первый символ каждой строки. Если они равны друг другу, они будут находиться в следующих парах до тех пор, пока символы не будут меньше или пока не достигнет конца укороченной строки.

## <a name="syntax"></a>Синтаксис

`strcmp(`*строка1* `,` *строка2*`)` 

## <a name="arguments"></a>Аргументы

* *строка1*: первая входная строка для сравнения. 
* *строка2*: Вторая входная строка для сравнения.

## <a name="returns"></a>Результаты

Возвращает целочисленное значение, указывающее связь между строками:
* *<0* — первый несовпадающий символ с меньшим значением строка1, чем в строка_замены
* *0* — содержимое обеих строк равно
* *>0* — первый символ, который не соответствует, имеет большее значение строка1, чем в строка_замены

## <a name="examples"></a>Примеры

```
datatable(string1:string, string2:string)
["ABC","ABC",
"abc","ABC",
"ABC","abc",
"abcde","abc"]
| extend result = strcmp(string1,string2)
```

|строка1|string2|result|
|---|---|---|
|ABC|ABC|0|
|abc|ABC|1|
|ABC|abc|-1|
|ABCDE|abc|1|