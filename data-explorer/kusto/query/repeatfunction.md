---
title: Повтор () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается повтор () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cfe3c2704f7eb086319770925419a9877e39366b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243080"
---
# <a name="repeat"></a>repeat()

Создает динамический массив, содержащий ряд равных значений.

## <a name="syntax"></a>Синтаксис

`repeat(`*значение* `,` *количество*`)` 

## <a name="arguments"></a>Аргументы

* *value*: значение элемента в результирующем массиве. Тип *значения* может быть логическим, целочисленным, длинным, реальным, DateTime или TimeSpan.   
* *Count*: количество элементов в результирующем массиве. Число *должно быть* целым числом.
Если значение *Count* равно нулю, возвращается пустой массив.
Если *Count* меньше нуля, возвращается значение null. 

## <a name="examples"></a>Примеры

В следующем примере возвращается `[1, 1, 1]`.

```kusto
T | extend r = repeat(1, 3)
```