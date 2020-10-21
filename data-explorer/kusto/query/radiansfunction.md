---
title: радианы () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается радианы () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bacb005b8828c63efac1737c527cc313a3ee052b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241321"
---
# <a name="radians"></a>radians()

Преобразует значение угла в градусах в значение в радианах, используя формулу `radians = (PI / 180 ) * angle_in_degrees`

## <a name="syntax"></a>Синтаксис

`radians(`*конкретного*`)`

## <a name="arguments"></a>Аргументы

* *а*: угол в градусах (вещественное число).

## <a name="returns"></a>Результаты

* Соответствующий угол в радианах для угла, указанного в градусах. 

## <a name="examples"></a>Примеры

```kusto
print radians0 = radians(90), radians1 = radians(180), radians2 = radians(360) 

```

|radians0|radians1|radians2|
|---|---|---|
|1,5707963267949|3,14159265358979)|6.28318530717959|