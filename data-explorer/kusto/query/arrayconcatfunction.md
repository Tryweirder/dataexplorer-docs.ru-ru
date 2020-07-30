---
title: array_concat () — обозреватель данных Azure
description: В этой статье описывается array_concat () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ecaca4aea221ca2b880b798757de64787901a0cb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349607"
---
# <a name="array_concat"></a>array_concat()

Сцепляет несколько динамических массивов в один массив.

## <a name="syntax"></a>Синтаксис

`array_concat(`*arr1* `[` , ` *arr2*, ...]` )

## <a name="arguments"></a>Аргументы

* *arr1... Аррн*: входные массивы, объединяемые в динамический массив. Все аргументы должны быть динамическими массивами (см. [pack_array](packarrayfunction.md)). 

## <a name="returns"></a>Результаты

Динамический массив массивов с arr1, arr2,..., Аррн.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend a1 = pack_array(x,y,z), a2 = pack_array(x, y)
| project array_concat(a1, a2)
```

|Column1|
|---|
|[1, 2, 4, 1, 2]|
|[2, 4, 8, 2, 4]|
|[3, 6, 12, 3, 6]|
