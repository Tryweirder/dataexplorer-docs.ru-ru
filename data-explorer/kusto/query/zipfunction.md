---
title: почтовый индекс () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описана застежка-молния () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd407ce652d41471be5b30a15c2c09b9f608edb1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504239"
---
# <a name="zip"></a>zip()

Функция `zip` принимает любое `dynamic` количество массивов и возвращает массив, элементы которого являются массивом, держащим элементы входных массивов одного и того же индекса.

**Синтаксис**

`zip(`*array1* `,` *array2*`, ... )`

**Аргументы**

От 2 до 16 динамических массивов.

**Примеры**

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