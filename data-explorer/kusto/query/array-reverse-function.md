---
title: array_reverse () — обозреватель данных Azure
description: В этой статье описывается array_reverse () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 327564a953c8f537a0f76727b4313749f61eec3e
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2020
ms.locfileid: "94374016"
---
# <a name="array_reverse"></a>array_reverse ()

Изменяет порядок элементов в динамическом массиве на обратный.

## <a name="syntax"></a>Синтаксис

`array_reverse(`*array*`)`

## <a name="arguments"></a>Аргументы

*массив* : входной массив для обращения.

## <a name="returns"></a>Возвращаемое значение

Массив, содержащий точно те же элементы, что и входной массив, но в противоположном порядке.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_reverse(arr)
```

|Результат|
|---|
|["example", "a", "-", "this"]|
