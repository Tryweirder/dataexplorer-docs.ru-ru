---
title: isempty () - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается пусто () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2cb0e53aa16257398c20661c31494ca9dda17c1e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513623"
---
# <a name="isempty"></a>isempty()

Возвращает, `true` если аргумент является пустой строкой или является нулевым.
    
```kusto
isempty("") == true
```

**Синтаксис**

`isempty(`Значение*value*`)`

**Возвращает**

Указывает, является ли аргумент пустой строкой или имеет значение null.

|x|isempty(x)
|---|---
| "" | Да
|"x" | false
|parsejson("")|Да
|parsejson("[]")|false
|парсейсон("){}|false

**Пример**

```kusto
T
| where isempty(fieldName)
| count
```