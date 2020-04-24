---
title: isascii() - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается isascii() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: daba0f4015a4847155309964f8ac0909ff4bc9d0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513725"
---
# <a name="isascii"></a>isascii()

Возвращает, `true` если аргумент является допустимой строкой ascii.
    
```kusto
isascii("some string") == true
```

**Синтаксис**

`isascii(`Значение*value*`)`

**Возвращает**

Указывает, является ли аргумент допустимой строкой ascii.

**Пример**

```kusto
T
| where isascii(fieldName)
| count
```