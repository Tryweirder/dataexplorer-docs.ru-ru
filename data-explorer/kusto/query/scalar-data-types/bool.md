---
title: Тип данных bool - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается тип данных bool в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: dd9cfa4f97f3baa4353f967f5e1ca31186f4815f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509934"
---
# <a name="the-bool-data-type"></a>Тип данных bool

Тип `bool` `boolean`данных может иметь одно из `true` `false` двух состояний: или `1` `0`(внутренне закодированкак и, соответственно), а также нулевую стоимость.

## <a name="bool-literals"></a>бул буквально

Тип `bool` данных имеет следующие буквы:
* `true`и `bool(true)`: Представление истинности
* `false`и `bool(false)`: Представление лжи
* `bool(null)`: См [нулевых значений](null-values.md)

## <a name="bool-operators"></a>операторы була

Тип `bool` данных поддерживает следующие`==`операторы:`!=`равенство (`and`), неравенство (`or`, логические и ( ), и логические или ().