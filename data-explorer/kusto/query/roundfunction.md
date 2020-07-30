---
title: Round () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается Round () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c281d3347e82b429ded187ee142ea13fa7594567
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345748"
---
# <a name="round"></a>round()

Возвращает округленный источник с заданной точностью.

## <a name="syntax"></a>Синтаксис

`round(`*Source* [ `,` *точность*]`)`

## <a name="arguments"></a>Аргументы

* *Source*: исходный скаляр, для которого вычисляется округление.
* *Точность*: количество разрядов, до которого будет округляться источник. (значение по умолчанию — 0)

## <a name="returns"></a>Результаты

Округленный источник с указанной точностью.

Метод Round отличается от первого округления [`bin()`](binfunction.md) / [`floor()`](floorfunction.md) числа до определенного числа разрядов, а Последнее значение округляет на целое число, кратное заданному размеру ячейки (Round (2.15, 1) возвращает 2,2, а bin (2.15, 1) возвращает 2).
 

## <a name="examples"></a>Примеры

```kusto
round(2.15, 1)                   // 2.2
round(2.15) (which is the same as round(2.15, 0))                   // 2
round(-50.55, -2)                   // -100
round(21.5, -1)                   // 20
```