---
title: array_length () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается array_length () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3777194270e91d75d68c3af544e18b62358f709e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246781"
---
# <a name="array_length"></a>array_length()

Вычисляет количество элементов в динамическом массиве.

## <a name="syntax"></a>Синтаксис

`array_length(`*array*`)`

## <a name="arguments"></a>Аргументы

* *Array*: `dynamic` значение.

## <a name="returns"></a>Результаты

Количество элементов в массиве *array* или `null`, если *array* не является массивом.

## <a name="examples"></a>Примеры

```kusto
print array_length(parse_json('[1, 2, 3, "four"]')) == 4

print array_length(parse_json('[8]')) == 1

print array_length(parse_json('[{}]')) == 1

print array_length(parse_json('[]')) == 0

print array_length(parse_json('{}')) == null

print array_length(parse_json('21')) == null
```