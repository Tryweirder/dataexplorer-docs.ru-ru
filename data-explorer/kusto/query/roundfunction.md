---
title: Round () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается Round () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 90d424929fe0b2034e4778ca2167e1e14dfbf79e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242905"
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