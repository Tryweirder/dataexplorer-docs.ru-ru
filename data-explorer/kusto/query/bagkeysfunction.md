---
title: bag_keys () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны bag_keys () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a73798dff27bf9f4c7d554d97804aef6b49e1de2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518213"
---
# <a name="bag_keys"></a>bag_keys()

Перечисляет все корневые клавиши в динамическом объекте с пакетом свойств.

`bag_keys(`*динамический объект*`)`

**Возвращает**

Массив ключей, порядок не определен.

**Примеры**

|Выражение|Результат|
|---|---|
|`bag_keys(dynamic({'a':'b', 'c':123}))` | `['a','c']`|
|`bag_keys(dynamic({'a':'b', 'c':{'d':123}})) `|`['a','c']`|
|`bag_keys(dynamic({'a':'b', 'c':[{'d':123}]})) `|`['a','c']`|
|`bag_keys(dynamic(null))`|`null`|
|`bag_keys(dynamic({}))`|`[]`|
|`bag_keys(dynamic('a'))`|`null`|
|`bag_keys(dynamic([]))  `|`null`|