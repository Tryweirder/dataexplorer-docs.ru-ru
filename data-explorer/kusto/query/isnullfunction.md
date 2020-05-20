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
ms.openlocfilehash: 4c57c7aba2bff2dfaecfa72b20ab76cc84ed17d6
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550594"
---
# <a name="isnull"></a>isnull()

Вычисляет свой единственный аргумент и возвращает `bool` значение, указывающее, имеет ли аргумент значение null.

```kusto
isnull(parse_json("")) == true
```

**Синтаксис**

`isnull(`*Выражения*`)`

**Возвращает**

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

**Пример**

```kusto
T | where isnull(PossiblyNull) | count
```