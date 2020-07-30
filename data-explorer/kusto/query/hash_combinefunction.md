---
title: hash_combine () — обозреватель данных Azure
description: В этой статье описывается hash_combine () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: f71a0d0cdfa4fe0ca8cdb84e65a271ee42bc7dc7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347618"
---
# <a name="hash_combine"></a>hash_combine()

Объединяет хэш-значения двух или более хэшей.

## <a name="syntax"></a>Синтаксис

`hash_combine(`*H1* `,` *H2* [ `,` *H3* ...]`)`

## <a name="arguments"></a>Аргументы

* *H1*: длинное значение, представляющее первое значение хэша.
* *H2*: длинное значение, представляющее второе хэш-значение.
* *HN*: длинное значение, представляющее n-значное значение.

## <a name="returns"></a>Возвращаемое значение

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
