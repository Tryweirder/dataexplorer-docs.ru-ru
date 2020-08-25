---
title: dynamic_to_json () — обозреватель данных Azure
description: В этой статье описывается dynamic_to_json () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 08/05/2020
ms.openlocfilehash: 94901a69b4c4435e983f39f1692cf45cf27756e5
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793789"
---
# <a name="dynamic_to_json"></a>dynamic_to_json()

Преобразует `dynamic` входные данные в строковое представление.
Если входные данные являются контейнером свойств, то выходная строка выводит содержимое, упорядоченное по ключам, рекурсивно. В противном случае выходные данные похожи на `tostring` выходные данные функции.

## <a name="syntax"></a>Синтаксис

`dynamic_to_json(Expr)`

## <a name="arguments"></a>Аргументы

* *Expr*: `dynamic` input. Функция принимает один аргумент.

## <a name="returns"></a>Возвращаемое значение

Возвращает строковое представление `dynamic` входных данных. Если входные данные являются контейнером свойств, то выходная строка выводит содержимое, упорядоченное по ключам, рекурсивно.

## <a name="examples"></a>Примеры

Выражение:

```kusto
  let bag1 = dynamic_to_json(dynamic({ 'Y10':dynamic({ }), 'X8': dynamic({ 'c3':1, 'd8':5, 'a4':6 }),'D1':114, 'A1':12, 'B1':2, 'C1':3, 'A14':[15, 13, 18]}));
  print bag1
```
  
Результат:

```
"{
  ""A1"": 12,
  ""A14"": [
    15,
    13,
    18
  ],
  ""B1"": 2,
  ""C1"": 3,
  ""D1"": 114,
  ""X8"": {
    ""c3"": 1,
    ""d8"": 5,
    ""a4"": 6
  },
  ""Y10"": {}
}"
```

Выражение:

```kusto
 let bag2 = dynamic_to_json(dynamic({ 'X8': dynamic({ 'a4':6, 'c3':1, 'd8':5}), 'A14':[15, 13, 18], 'C1':3, 'B1':2, 'Y10': dynamic({ }), 'A1':12, 'D1':114}));
 print bag2
```
 
Результат:

```
{
  "A1": 12,
  "A14": [
    15,
    13,
    18
  ],
  "B1": 2,
  "C1": 3,
  "D1": 114,
  "X8": {
    "a4": 6,
    "c3": 1,
    "d8": 5
  },
  "Y10": {}
}
```
