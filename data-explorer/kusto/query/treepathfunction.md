---
title: treepath () - Лазурный исследователь данных (ru) Документы Майкрософт
description: В этой статье описаны древесные пути () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 27a0edc61a1d2317427454aaf74531ec395d067d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505701"
---
# <a name="treepath"></a>treepath()

Перечисляет все выражения пути, которые определяют листья в динамическом объекте.

`treepath(`*динамический объект*`)`

**Возвращает**

Массив выражений пути.

**Примеры**

|Выражение|Результат|
|---|---|
|`treepath(parse_json('{"a":"b", "c":123}'))` | `["['a']","['c']"]`|
|`treepath(parse_json('{"prop1":[1,2,3,4], "prop2":"value2"}'))`|`["['prop1']","['prop1'][0]","['prop2']"]`|
|`treepath(parse_json('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))`|`["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]`|