---
title: array_iif () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается array_iif () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/28/2019
ms.openlocfilehash: 1844d87dffb5ac0046c3f62600b8c4914dc93a89
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349590"
---
# <a name="array_iif"></a>array_iif()

Элементная функция IIf для динамических массивов.

Другой псевдоним: array_iff ().

## <a name="syntax"></a>Синтаксис

`array_iif(`*Кондитионаррай*, *IfTrue*, *IfFalse*]`)`

## <a name="arguments"></a>Аргументы

* *кондитионаррай*: входной массив *логических* или числовых значений должен быть динамическим массивом.
* *IfTrue*: входной массив значений или примитивного значения — результирующие значения, если соответствующее значение *кондитионаррай* равно *true*.
* *IfFalse*: входной массив значений или примитивного значения — результирующие значения, если соответствующее значение *кондитионаррай* равно *false*.

**Примечания**

* Длина результата — это длина *кондитионаррай*.
* Числовое значение условия обрабатывается как *Condition* ! = *0*.
* Значение условия, отличное от numeric или null, будет иметь значение NULL в соответствующем индексе результата.
* Отсутствующие значения (в массивах более короткой длины) обрабатываются как значения NULL.

## <a name="returns"></a>Возвращаемое значение

Динамический массив значений, взятых из значений *IfTrue* или *IfFalse* [Array] в соответствии с соответствующим значением массива условий.

## <a name="example"></a>Пример

```kusto
print condition=dynamic([true,false,true]), l=dynamic([1,2,3]), r=dynamic([4,5,6]) 
| extend res=array_iif(condition, l, r)
```

|condition|l|r|res|
|---|---|---|---|
|[true, false, true]|[1, 2, 3]|[4, 5, 6]|[1, 5, 3]|
