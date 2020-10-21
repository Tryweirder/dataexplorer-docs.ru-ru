---
title: strcat_array () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается strcat_array () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 06bdf7eb55b61cb974e803c9cdeb7b37b34ad87e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243795"
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