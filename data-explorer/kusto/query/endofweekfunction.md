---
title: ендофвик () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается ендофвик () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 57fa1764753e730f9ff0a2b01a70e0c221217d23
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348281"
---
# <a name="endofweek"></a>endofweek()

Возвращает конец недели, содержащий дату, смещенную по смещению, если оно предоставлено.

Последний день недели считается субботу.

## <a name="syntax"></a>Синтаксис

`endofweek(`*Дата* [ `,` *смещение*]`)`

## <a name="arguments"></a>Аргументы

* `date`: Входная дата.
* `offset`: Необязательное количество смещенных недель с даты ввода (целое число, по умолчанию — 0).

## <a name="returns"></a>Результаты

Значение типа DateTime, представляющее конец недели для заданного значения *даты* и смещение, если оно указано.

## <a name="example"></a>Пример

```kusto
  range offset from -1 to 1 step 1
 | project weekEnd = endofweek(datetime(2017-01-01 10:10:17), offset)  

```

|затенен|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-07 23:59:59.9999999|
|2017-01-14 23:59:59.9999999|