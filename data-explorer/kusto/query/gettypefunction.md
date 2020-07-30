---
title: GetType () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается GetType () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 0efa07b7a1b050fe81ce2f369e8df5af4c05e212
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347669"
---
# <a name="gettype"></a>gettype()

Возвращает тип отдельного аргумента среды выполнения.

Тип среды выполнения может отличаться от номинального (статического) типа для выражений, номинальным типом которых является `dynamic` ; в таких случаях `gettype()` может быть полезно отобразить тип групповой фактического значения (способ кодирования значения в памяти).

## <a name="syntax"></a>Синтаксис

`gettype(`*Выражения*`)`

## <a name="returns"></a>Результаты

Строка, представляющая тип отдельного аргумента среды выполнения.

## <a name="examples"></a>Примеры

|Выражение                          |Результаты      |
|------------------------------------|-------------|
|`gettype("a")`                      |`string`     |
|`gettype(111)`                      |`long`       |
|`gettype(1==1)`                     |`bool`       |
|`gettype(now())`                    |`datetime`   |
|`gettype(1s)`                       |`timespan`   |
|`gettype(parse_json('1'))`           |`int`        |
|`gettype(parse_json(' "abc" '))`     |`string`     |
|`gettype(parse_json(' {"abc":1} '))` |`dictionary` | 
|`gettype(parse_json(' [1, 2, 3] '))` |`array`      |
|`gettype(123.45)`                   |`real`       |
|`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))`|`guid`| 
|`gettype(parse_json(''))`            |`null`|