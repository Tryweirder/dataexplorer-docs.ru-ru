---
title: ACOS () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается ACOS () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 486be5487071fa281765de410fe4e5be1ce62a38
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253124"
---
# <a name="acos"></a>acos()

Возвращает угол, косинус которого равен указанному числу (операция обратного [`cos()`](cosfunction.md) ).

## <a name="syntax"></a>Синтаксис

`acos(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число в диапазоне [-1, 1].

## <a name="returns"></a>Результаты

* Значение косинуса дуги `x`
* `null` Если `x` <-1 или `x` > 1