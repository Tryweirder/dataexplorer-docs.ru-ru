---
title: градусы () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается градусы () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 04efbee59bce153de76ab5d44617b8d1bebc121c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253092"
---
# <a name="degrees"></a>degrees()

Преобразует значение угла в радианах в значение в градусах, используя формулу `degrees = (180 / PI ) * angle_in_radians`

## <a name="syntax"></a>Синтаксис

`degrees(`*конкретного*`)`

## <a name="arguments"></a>Аргументы

* *a*: угол в радианах (вещественное число).

## <a name="returns"></a>Результаты

* Соответствующий угол в градусах для угла, указанного в радианах. 

## <a name="examples"></a>Примеры

```kusto
print degrees0 = degrees(pi()/4), degrees1 = degrees(pi()*1.5), degrees2 = degrees(0)

```

|degrees0|degrees1|degrees2|
|---|---|---|
|45|270|0|
