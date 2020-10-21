---
title: hash_combine () — обозреватель данных Azure
description: В этой статье описывается hash_combine () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: 91a178ded007ff75d96ed73f864276aa7d193d8c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244779"
---
# <a name="hash_combine"></a>hash_combine()

Объединяет хэш-значения двух или более хэшей.

## <a name="syntax"></a>Синтаксис

`hash_combine(`*H1* `,` *H2* [ `,` *H3* ...]`)`

## <a name="arguments"></a>Аргументы

* *H1*: длинное значение, представляющее первое значение хэша.
* *H2*: длинное значение, представляющее второе хэш-значение.
* *HN*: длинное значение, представляющее n-значное значение.

## <a name="returns"></a>Результаты

Объединенное хэш-значение заданных скаляров.

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|value1|value2|H1|H2|вышают|
|---|---|---|---|---|
|Привет|World|753694413698530628|1846988464401551951|— 1440138333540407281|
