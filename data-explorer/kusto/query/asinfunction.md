---
title: ASIN () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается ASIN () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 360a936d136cd01760175cdf5b5da2718d0cfd91
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349505"
---
# <a name="asin"></a>asin()

Возвращает угол, синус которого равен указанному числу (операция обратного [`sin()`](sinfunction.md) ).

## <a name="syntax"></a>Синтаксис

`asin(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число в диапазоне [-1, 1].

## <a name="returns"></a>Результаты

* Значение синуса дуги`x`
* `null`Если `x` <-1 или `x` > 1