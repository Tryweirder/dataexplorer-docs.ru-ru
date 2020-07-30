---
title: IsNull () — Azure обозреватель данных
description: В этой статье описывается функция IsNull () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d1bea6260ca86e6ca47be843a6acc4fb43a037b3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347176"
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

* `string`значения не могут быть неопределенными. Чтобы определить, является ли значение типа `string` пустым, используйте параметр [isempty](./isemptyfunction.md).

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