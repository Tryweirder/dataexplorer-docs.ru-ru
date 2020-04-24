---
title: isnotempty() - Azure Data Explorer Документы Майкрософт
description: В этой статье описано непусто() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 14111be0fc0247dd151ef7454121e6b90a32ff0d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513538"
---
# <a name="isnotempty"></a>isnotempty()

Возвращает, `true` если аргумент не является пустой строкой, и он не является нулевым.

```kusto
isnotempty("") == false
```

**Синтаксис**

`isnotempty(`Значение*value*`)`

`notempty(`Значение*value* `)` -- псевдоним`isnotempty`