---
title: array_length () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны array_length () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0bb1daeba6d24f8bd7326fcd0b8c17f06003e30b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518604"
---
# <a name="array_length"></a>array_length()

Вычисляет количество элементов в динамическом массиве.

**Синтаксис**

`array_length(`*Массива*`)`

**Аргументы**

* *массив*: `dynamic` Значение.

**Возвращает**

Количество элементов в массиве *array* или `null`, если *array* не является массивом.

**Примеры**

```kusto
print array_length(parse_json('[1, 2, 3, "four"]')) == 4

print array_length(parse_json('[8]')) == 1

print array_length(parse_json('[{}]')) == 1

print array_length(parse_json('[]')) == 0

print array_length(parse_json('{}')) == null

print array_length(parse_json('21')) == null
```