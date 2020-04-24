---
title: потолок () - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается потолок () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f2ecd043c43bb1af6530364d200d5dc9db640f95
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517261"
---
# <a name="ceiling"></a>ceiling()

Вычисляет наименьшее число больше, чем указанное числовое выражение.

**Синтаксис**

`ceiling(`*X*`)`

**Аргументы**

* *x*: Реальное число.

**Возвращает**

* Наименьшее число больше, чем указанное числовое выражение. 

**Примеры**

```kusto
print c1 = ceiling(-1.1), c2 = ceiling(0), c3 = ceiling(0.9)
```

|c1|c2|c3|
|---|---|---|
|-1|0|1|