---
title: strcat_array () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается strcat_array () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b8369d2e994477c9d01880632fac5f8a3ebaf6a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342603"
---
# <a name="strcat_array"></a>strcat_array()

Создает объединенную строку значений массива с помощью указанного разделителя.
    
## <a name="syntax"></a>Синтаксис

`strcat_array(`*массив*, *Разделитель*`)`

## <a name="arguments"></a>Аргументы

* *Array*: `dynamic` значение, представляющее Массив объединяемых значений.
* *разделителем*: `string` значение, которое будет использоваться для сцепления значений в *массиве*

## <a name="returns"></a>Результаты

Значения массива, Объединенные в одну строку.

## <a name="examples"></a>Примеры
  
```kusto
print str = strcat_array(dynamic([1, 2, 3]), "->")
```

|str|
|---|
|1->2 — >3|