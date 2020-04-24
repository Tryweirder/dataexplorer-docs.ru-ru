---
title: тогекс () - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны тогексы () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 402a0923d4fe760e97fa6098ad955b6c24a5d5ee
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506126"
---
# <a name="tohex"></a>tohex()

Преобразует вход в гексадецимальную строку.

```kusto
tohex(256) == '100'
tohex(-256) == 'ffffffffffffff00' // 64-bit 2's complement of -256
tohex(toint(-256), 8) == 'ffffff00' // 32-bit 2's complement of -256
tohex(256, 8) == '00000100'
tohex(256, 2) == '100' // Exceeds min length of 2, so min length is ignored.
```

**Синтаксис**

`tohex(`*Экстр*`, [`` *MinLength*]`,)'

**Аргументы**

* *Expr*: Int или длинное значение, которое будет преобразовано в гекс строку.  Другие типы не поддерживаются.
* *MinLength*: числовое значение, представляющее количество ведущих символов для включения в вывод.  Значения от 1 до 16 поддерживаются, значения, превышающее 16, будут усечены до 16.  Если строка длиннее минДлина без ведущих персонажей, то minLength фактически игнорируется.  Отрицательные числа могут быть представлены как минимум по размеру данных, поэтому для int (32-битный) минДлина будет не менее 8, для длинного (64-битного) она будет как минимум 16.

**Возвращает**

Если преобразование успешно, результат будет значение строки.
Если конверсия не будет успешной, результат будет нулевым.