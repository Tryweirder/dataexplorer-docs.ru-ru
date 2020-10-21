---
title: ASIN () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается ASIN () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e23ddda4bbc2e165adfd810e1dc27b5ffd14edf4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246738"
---
# <a name="asin"></a>asin()

Возвращает угол, синус которого равен указанному числу (операция обратного [`sin()`](sinfunction.md) ).

## <a name="syntax"></a>Синтаксис

`asin(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число в диапазоне [-1, 1].

## <a name="returns"></a>Результаты

* Значение синуса дуги `x`
* `null` Если `x` <-1 или `x` > 1