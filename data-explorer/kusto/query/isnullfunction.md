---
title: IsNull () — Azure обозреватель данных
description: В этой статье описывается функция IsNull () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: afaff2c00ca9136e113639deed886d039d21fda9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241528"
---
# <a name="isnull"></a>isnull()

Вычисляет свой единственный аргумент и возвращает `bool` значение, указывающее, имеет ли аргумент значение null.

```kusto
isnull(parse_json("")) == true
```

## <a name="syntax"></a>Синтаксис

`isnull(`*Выражения*`)`

## <a name="returns"></a>Результаты

Значение true или false в зависимости от того, является ли значение нулевым.

**Примечания**

* `string` значения не могут быть неопределенными. Чтобы определить, является ли значение типа `string` пустым, используйте параметр [isempty](./isemptyfunction.md).

|x                |`isnull(x)`|
|-----------------|-----------|
|`""`             |`false`    |
|`"x"`            |`false`    |
|`parse_json("")`  |`true`     |
|`parse_json("[]")`|`false`    |
|`parse_json("{}")`|`false`    |

## <a name="example"></a>Пример

```kusto
T | where isnull(PossiblyNull) | count
```