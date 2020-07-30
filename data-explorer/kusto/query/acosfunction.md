---
title: ACOS () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается ACOS () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d1daf36e85eec4c8543ba14be153a9d6069e1cd1
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349862"
---
# <a name="acos"></a>acos()

Возвращает угол, косинус которого равен указанному числу (операция обратного [`cos()`](cosfunction.md) ).

## <a name="syntax"></a>Синтаксис

`acos(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число в диапазоне [-1, 1].

## <a name="returns"></a>Результаты

* Значение косинуса дуги`x`
* `null`Если `x` <-1 или `x` > 1