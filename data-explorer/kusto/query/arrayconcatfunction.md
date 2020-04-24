---
title: array_concat () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описывается array_concat () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: c66c17ab147eb3d6c5f749e7f28fad347a50ce22
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518757"
---
# <a name="array_concat"></a>array_concat()

Конкатирует ряд динамических массивов в один массив.

**Синтаксис**

`array_concat(`*arr1*`[`` *arr2*, ...]`,)'

**Аргументы**

* *arr1... arrN*: Входные массивы должны быть скатированы в динамический массив. Все аргументы должны быть динамическими массивами [(см. pack_array).](packarrayfunction.md) 

**Возвращает**

Динамический массив массивов с arr1, arr2, ... , arrN.

**Пример**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend a1 = pack_array(x,y,z), a2 = pack_array(x, y)
| project array_concat(a1, a2)
```

|Column1|
|---|
|[1,2,4,1,2]|
|[2,4,8,2,4]|
|[3,6,12,3,6]|