---
title: ZIP () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается ZIP () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 28fc477d4dfc5432434261e493f36985514ea28b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350559"
---
# <a name="zip"></a>zip()

`zip`Функция принимает любое количество `dynamic` массивов и возвращает массив, элементы которого содержат массивы элементов входных массивов одного индекса.

## <a name="syntax"></a>Синтаксис

`zip(`*массив1* `,` *массив2*`, ... )`

## <a name="arguments"></a>Аргументы

Между 2 и 16 динамическими массивами.

## <a name="examples"></a>Примеры

В следующем примере возвращается `[[1,2],[3,4],[5,6]]`.

```kusto
print zip(dynamic([1,3,5]), dynamic([2,4,6]))
```

В следующем примере возвращается `[["A",{}], [1,"B"], [1.5, null]]`.

```kusto
print zip(dynamic(["A", 1, 1.5]), dynamic([{}, "B"]))
```

В следующем примере возвращается `[[1,"one"],[2,"two"],[3,"three"]]`.

```kusto
datatable(a:int, b:string) [1,"one",2,"two",3,"three"]
| summarize a = make_list(a), b = make_list(b)
| project zip(a, b)
```