---
title: тохекс () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается тохекс () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7784424d0053761d4cfdb373dea0ed57f8bca83e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250368"
---
# <a name="tohex"></a>tohex()

Преобразует входные данные в шестнадцатеричную строку.

```kusto
tohex(256) == '100'
tohex(-256) == 'ffffffffffffff00' // 64-bit 2's complement of -256
tohex(toint(-256), 8) == 'ffffff00' // 32-bit 2's complement of -256
tohex(256, 8) == '00000100'
tohex(256, 2) == '100' // Exceeds min length of 2, so min length is ignored.
```

## <a name="syntax"></a>Синтаксис

`tohex(`*Expr* `, [` , ` *MinLength*]` ) '

## <a name="arguments"></a>Аргументы

* *Expr*: int или длинное значение, которое будет преобразовано в шестнадцатеричную строку.  Другие типы не поддерживаются.
* *MinLength*: числовое значение, представляющее число начальных символов, включаемых в выход.  Поддерживаются значения от 1 до 16, а значения больше 16 будут усечены до 16.  Если строка длиннее minLength без ведущих символов, то параметр minLength фактически игнорируется.  Отрицательные числа могут быть представлены как минимум базовым размером данных, поэтому для типа int (32-bit) значение minLength будет равно минимум 8, для длинного (64-разрядного) — не менее 16.

## <a name="returns"></a>Результаты

Если преобразование выполнено успешно, результатом будет строковое значение.
Если преобразование не выполнено, результатом будет NULL.