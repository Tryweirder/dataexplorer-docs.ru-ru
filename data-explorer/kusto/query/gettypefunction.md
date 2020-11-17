---
title: GetType () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается GetType () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 84aa0d815328532f7a627958b56c41b998665d6b
ms.sourcegitcommit: f7bebd245081a5cdc08e88fa4f9a769c18e13e5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94644609"
---
# <a name="gettype"></a>gettype()

Возвращает тип отдельного аргумента среды выполнения.

Тип среды выполнения может отличаться от номинального (статического) типа для выражений, номинальным типом которых является `dynamic` ; в таких случаях `gettype()` может быть полезно отобразить тип фактического значения (способ кодирования значения в памяти).

## <a name="syntax"></a>Синтаксис

`gettype(`*Выражения*`)`

## <a name="returns"></a>Возвращаемое значение

Строка, представляющая тип отдельного аргумента среды выполнения.

## <a name="examples"></a>Примеры

|Expression                          |Возвращаемое значение      |
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
