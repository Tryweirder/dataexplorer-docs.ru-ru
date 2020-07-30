---
title: -ASCII () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются символы-ASCII () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d8a060e4a332988fd966e0dec9ed07b3c76d0e3f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347295"
---
# <a name="isascii"></a>isascii()

Возвращает значение, `true` Если аргумент является допустимой строкой ASCII.
    
```kusto
isascii("some string") == true
```

## <a name="syntax"></a>Синтаксис

`isascii(`[*значение*]`)`

## <a name="returns"></a>Результаты

Указывает, является ли аргумент допустимой строкой ASCII.

## <a name="example"></a>Пример

```kusto
T
| where isascii(fieldName)
| count
```