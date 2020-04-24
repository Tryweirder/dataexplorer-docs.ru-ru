---
title: sqrt() - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описаны sqrt() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 660235a60893732288a551e1febd9b7b044b4f00
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507265"
---
# <a name="sqrt"></a>sqrt()

Возвращает функцию квадратного корня.  

**Синтаксис**

`sqrt(`*X*`)`

**Аргументы**

* *x*: Реальное число >0.

**Возвращает**

* Положительное число, такое как `sqrt(x) * sqrt(x) == x`
* `null`, если аргумент является отрицательным числом или не может быть преобразован в значение `real`. 