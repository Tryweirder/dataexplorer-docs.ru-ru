---
title: ранд () - Исследователь данных Azure (ru) Документы Майкрософт
description: Эта статья описывает ранд () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d3638d4b979b7318f58efec0bed0da4c31896a9d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510648"
---
# <a name="rand"></a>rand()

Возвращает случайное число.

```kusto
rand()
rand(1000)
```

**Синтаксис**

* `rand()`- возвращает значение `real` типа с единым распределением в диапазоне 0,0, 1,0).
* `rand(`*N* `)` - возвращает значение `real` выбранного типа с единым распределением из набора 0,0, 1,0, ..., *N* - 1".