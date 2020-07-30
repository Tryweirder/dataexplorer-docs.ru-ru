---
title: hash_sha256 () — обозреватель данных Azure
description: В этой статье описывается hash_sha256 () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b813ce4c0901ef66177e8e7bdaa42a1744bd5912
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351120"
---
# <a name="hash_sha256"></a>hash_sha256()

Возвращает значение хэша SHA256 для входного значения.

## <a name="syntax"></a>Синтаксис

`hash_sha256(`*источника*`)`

## <a name="arguments"></a>Аргументы

* *Source*: значение, которое необходимо хэшировать.

## <a name="returns"></a>Возвращаемое значение

Значение хэша SHA256 данного скаляра, закодированное как шестнадцатеричная строка (строка символов, каждая из которых представляет одно шестнадцатеричное число от 0 до 255).

> [!WARNING]
> Алгоритм, используемый этой функцией (SHA256), гарантированно не изменяется в будущем, но он очень сложен для вычисления. Пользователям, которым требуется "упрощенная" хэш-функция в течение одного запроса, рекомендуется использовать функцию [Hash ()](./hashfunction.md) .

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print 
h1=hash_sha256("World"),
h2=hash_sha256(datetime(2020-01-01))
```

|H1|H2|
|---|---|
|78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524|ba666752dc1a20eb750b0eb64e780cc4c968bc9fb8813461c1d7e750f302d71d|

В следующем примере функция используется `hash_sha256()` для статистической обработки стормевентс на основе значения ХЭША SHA256 состояния. 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count() by State, StateHash=hash_sha256(State)
| top 5 by StormCount desc
```

|Состояние|статехаш|стормкаунт|
|---|---|---|
|Техас|9087f20f23f91b5a77e8406846117049029e6798ebbd0d38aea68da73a00ca37|4701|
|Канзас|c80e328393541a3181b258cdb4da4d00587c5045e8cf3bb6c8fdb7016b69cc2e|3166|
|Айова|f85893dca466f779410f65cd904fdc4622de49e119ad4e7c7e4a291ceed1820b|2337|
|Иллинойс|ae3eeabfd7eba3d9a4ccbfed6a9b8cff269dc43255906476282e0184cf81b7fd|2022|
|Миссури|d15dfc28abc3ee73b7d1f664a35980167ca96f6f90e034db2a6525c0b8ba61b1|2016|
