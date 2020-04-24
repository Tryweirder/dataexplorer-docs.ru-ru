---
title: асин () - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается asin() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 5db112daeba59dd841b02df8ba1a41185654ad6a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518553"
---
# <a name="asin"></a>asin()

Возвращает угол, синусоида которого является указанным числом (обратная [`sin()`](sinfunction.md)операция).

**Синтаксис**

`asin(`*X*`)`

**Аргументы**

* *x*: Реальное число в диапазоне No-1, 1".

**Возвращает**

* Значение дуговой синусовой части`x`
* `null`если `x` < -1 или `x` > 1