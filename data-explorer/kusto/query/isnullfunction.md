---
title: isnull() - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается isnull() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e26dca661ceac1ad209358b24b3f8d497a5c3049
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513419"
---
# <a name="isnull"></a>isnull()

Оценивает его единственный `bool` аргумент и возвращает значение, указывающее, оценивается ли аргумент в нулевую величину.

```kusto
isnull(parse_json("")) == true
```

**Синтаксис**

`isnull(`*Expr*`)`

**Возвращает**

Значение true или false в зависимости от того, равен ли аргумент null.

**Примечания**

* `string`значения не могут быть нулевыми. Чтобы определить, является ли значение типа `string` пустым, используйте параметр [isempty](./isemptyfunction.md).

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