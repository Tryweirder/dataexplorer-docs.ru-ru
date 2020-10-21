---
title: a UTF8 () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается, как в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c9ad35964eb6d5a8c4a38b5ecdeb1eae43221d52
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247336"
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