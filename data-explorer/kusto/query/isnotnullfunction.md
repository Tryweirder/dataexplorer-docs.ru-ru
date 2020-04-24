---
title: isnotnull() - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны ненулевые () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8be57f2f7484081ac316a8af6fea252a60f895a4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513453"
---
# <a name="isnotnull"></a>isnotnull()

Возвращает, `true` если аргумент не является недействительным.

**Синтаксис**

`isnotnull(`Значение*value*`)`

`notnull(`Значение*value* `)` - псевдоним для`isnotnull`

**Пример**

```kusto
T | where isnotnull(PossiblyNull) | count
```

Обратите внимание, что того же эффекта можно добиться другими способами:

```kusto
T | summarize count(PossiblyNull)
```