---
title: array_length () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается array_length () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 14203e3078b7fe30222ea26320ed1391000d5c05
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349556"
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