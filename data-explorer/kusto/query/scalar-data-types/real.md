---
title: Реальный тип данных - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается реальный тип данных в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: f69b74670fefcff6f6c24d5235f58beb98b0405a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509679"
---
# <a name="the-real-data-type"></a>Реальный тип данных

Тип `real` данных представляет собой 64-разрядный широкий, двухточный, плавающий номер точки.

Буквы типа `real` данных имеют такое же представление, что и . NET's `System.Double`. `1.0`, `0.1`, `1e5` и все буквальные `real`типа .

Существует несколько специальных буквальных форм:
* `real(null)`: Является [нулевая стоимость](null-values.md).
* `real(nan)`: Не-номер (NaN). Например, результат деления `0.0` другого. `0.0`
* `real(+inf)`: Позитивная бесконечность. Например, результат деления `1.0` `0.0`на .
* `real(-inf)`: Отрицательная бесконечность. Например, результат деления `-1.0` `0.0`на .