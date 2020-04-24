---
title: acos() - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны акосы () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: aa33714d57b319ba5a775385e8ee7d232addfe9d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519335"
---
# <a name="acos"></a>acos()

Возвращает угол, cosine которого является указанным [`cos()`](cosfunction.md)числом (обратная операция ) .

**Синтаксис**

`acos(`*X*`)`

**Аргументы**

* *x*: Реальное число в диапазоне No-1, 1".

**Возвращает**

* Значение дуговой козины`x`
* `null`если `x` < -1 или `x` > 1