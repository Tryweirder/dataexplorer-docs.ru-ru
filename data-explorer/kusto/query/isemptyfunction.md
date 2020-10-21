---
title: IsEmpty () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается процедура IsEmpty () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2944e90f14f38e2f136f5815a95584edc50d8235
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251729"
---
# <a name="isempty"></a>isempty()

Возвращает, `true` Если аргумент является пустой строкой или имеет значение null.
    
```kusto
isempty("") == true
```

## <a name="syntax"></a>Синтаксис

`isempty(`[*значение*]`)`

## <a name="returns"></a>Результаты

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