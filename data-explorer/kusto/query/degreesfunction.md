---
title: градусы () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается градусы () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41d679ea1add3706de5012f4e4fbf382e1f7b3ee
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348383"
---
# <a name="degrees"></a>degrees()

Преобразует значение угла в радианах в значение в градусах, используя формулу`degrees = (180 / PI ) * angle_in_radians`

## <a name="syntax"></a>Синтаксис

`degrees(`*конкретного*`)`

## <a name="arguments"></a>Аргументы

* *a*: угол в радианах (вещественное число).

## <a name="returns"></a>Возвращаемое значение

* Соответствующий угол в градусах для угла, указанного в радианах. 

## <a name="examples"></a>Примеры

```kusto
print degrees0 = degrees(pi()/4), degrees1 = degrees(pi()*1.5), degrees2 = degrees(0)

```

|degrees0|degrees1|degrees2|
|---|---|---|
|45|270|0|
