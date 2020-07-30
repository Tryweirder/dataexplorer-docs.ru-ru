---
title: hash_md5 () — обозреватель данных Azure
description: В этой статье описывается hash_md5 () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/29/2020
ms.openlocfilehash: 17b3e0d13f8048ccac90add4eee6f02f3d2e2959
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346802"
---
# <a name="hash_md5"></a>hash_md5()

Возвращает хэш-значение MD5 для входного значения.

## <a name="syntax"></a>Синтаксис

`hash_md5(`*источника*`)`

## <a name="arguments"></a>Аргументы

* *Source*: значение, которое необходимо хэшировать.

## <a name="returns"></a>Результаты

Хэш-значение MD5 для данного скаляра, закодированное как шестнадцатеричная строка (строка символов, каждая из которых представляет одно шестнадцатеричное число от 0 до 255).

> [!WARNING]
> Алгоритм, используемый этой функцией (MD5), гарантированно не изменяется в будущем, но он очень сложен для вычисления. Пользователям, которым требуется "упрощенная" хэш-функция в течение одного запроса, рекомендуется использовать функцию [Hash ()](./hashfunction.md) .

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print 
h1=hash_md5("World"),
h2=hash_md5(datetime(2020-01-01))
```

|H1|H2|
|---|---|
|f5a7924e621e84c9280a9a27e1bcb7f6|786c530672d1f8db31fee25ea8a9390b|


В следующем примере функция используется `hash_md5()` для статистической обработки стормевентс на основе хэш-значения MD5 состояния. 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize StormCount = count() by State, StateHash=hash_md5(State)
| top 5 by StormCount
```

|Состояние|статехаш|стормкаунт|
|---|---|---|
|Техас|3b00dbe6e07e7485a1c12d36c8e9910a|4701|
|Канзас|e1338d0ac8be43846cf9ae967bd02e7f|3166|
|Айова|6d4a7c02942f093576149db764d4e2d2|2337|
|Иллинойс|8c00d9e0b3fcd55aed5657e42cc40cf1|2022|
|Миссури|2d82f0c963c0763012b2539d469e5008|2016|
