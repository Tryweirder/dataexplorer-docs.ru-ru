---
title: ZIP () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается ZIP () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33b914babb8c197f997326cd6dd44654c05d1d9d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253132"
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