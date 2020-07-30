---
title: объединение () — Azure обозреватель данных
description: В этой статье описывается объединение () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 410a0c84a1bafdfa1900ef8e21bc0a91327b64c3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348876"
---
# <a name="coalesce"></a>coalesce()

Вычисляет список выражений и возвращает первое выражение, отличное от NULL (или не пустое для строки).

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

## <a name="syntax"></a>Синтаксис

`coalesce(`*expr_1* `, ` *expr_2* `,` ...)

## <a name="arguments"></a>Аргументы

* *expr_i*— скалярное выражение для вычисления.
- Все аргументы должны быть одного типа.
- Поддерживается не более 64 аргументов.


## <a name="returns"></a>Результаты

Значение первого *expr_i* , значение которого не равно null (или не является пустым для строковых выражений).

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|result|
|---|
|42|
