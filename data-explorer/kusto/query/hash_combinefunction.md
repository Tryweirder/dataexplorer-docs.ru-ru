---
title: hash_combine () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны hash_combine() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: d0c6375436df298a97c03ec2f06f7cd492d59d7f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514269"
---
# <a name="hash_combine"></a>hash_combine()

Комбинирует хэш-значения двух или более хэшов.

**Синтаксис**

`hash_combine(`*h1* `,` *h2* -`,` *h3* ..."`)`

**Аргументы**

* *h1*: Длинное значение, представляющее первое значение хэша.
* *h2*: Длинное значение, представляющее второе значение хэша.
* *hN*: Длинное значение, представляющее значение nth хэша.

**Возвращает**

Совокупная хэш-значение данных скаляров.

**Примеры**

```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|value1|value2|h1|h2|Комбинированный|
|---|---|---|---|---|
|Привет|World|753694413698530628|1846988464401551951|-1440138333540407281|