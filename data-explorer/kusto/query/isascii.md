---
title: -ASCII () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются символы-ASCII () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a17836597277b2cf5401f2caeaa60b44da731dd5
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251783"
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