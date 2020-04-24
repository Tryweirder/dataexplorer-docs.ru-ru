---
title: not() - Azure Data Explorer Документы Майкрософт
description: В этой статье нет () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 539e409a9e922afc390b097c863146b7fc30d7b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512042"
---
# <a name="not"></a>not()

Меняет значение его `bool` аргумента.

```kusto
not(false) == true
```

**Синтаксис**

`not(`*Expr*`)`

**Аргументы**

* *expr*: `bool` Выражение, необходимое для обращения вспять.

**Возвращает**

Возвращает обратное логическое `bool` значение его аргумента.