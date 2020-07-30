---
title: a UTF8 () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается, как в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 952ea030d351a9e23fe26bbd7f27a96d182a89e3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347159"
---
# <a name="isutf8"></a>isutf8()

Возвращает значение, `true` Если аргумент является допустимой строкой UTF8.
    
```kusto
isutf8("some string") == true
```

## <a name="syntax"></a>Синтаксис

`isutf8(`[*значение*]`)`

## <a name="returns"></a>Результаты

Указывает, является ли аргумент допустимой строкой UTF8.

## <a name="example"></a>Пример

```kusto
T
| where isutf8(fieldName)
| count
```