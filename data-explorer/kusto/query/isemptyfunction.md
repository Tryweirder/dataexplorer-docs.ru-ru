---
title: IsEmpty () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается процедура IsEmpty () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ac2bf5d5ea55172cbdb07bf90704ae5ad497e925
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347278"
---
# <a name="isempty"></a>isempty()

Возвращает, `true` Если аргумент является пустой строкой или имеет значение null.
    
```kusto
isempty("") == true
```

## <a name="syntax"></a>Синтаксис

`isempty(`[*значение*]`)`

## <a name="returns"></a>Возвращаемое значение

Указывает, является ли аргумент пустой строкой или имеет значение null.

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("[]")|false
|parseJSON (" {} ")|false

## <a name="example"></a>Пример

```kusto
T
| where isempty(fieldName)
| count
```