---
title: isutf8() - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описана isutf8() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 619fb90b72fed8ec0e10fe05ddc3c6df6ff1386e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513402"
---
# <a name="isutf8"></a>isutf8()

Возвращает, `true` если аргумент является действительным строки utf8.
    
```kusto
isutf8("some string") == true
```

**Синтаксис**

`isutf8(`Значение*value*`)`

**Возвращает**

Указывает, является ли аргумент допустимой строкой utf8.

**Пример**

```kusto
T
| where isutf8(fieldName)
| count
```