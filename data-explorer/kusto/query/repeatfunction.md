---
title: Повтор () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается повтор () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0d488ac96cd3db2161761ea837d5490d25cfc920
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345833"
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