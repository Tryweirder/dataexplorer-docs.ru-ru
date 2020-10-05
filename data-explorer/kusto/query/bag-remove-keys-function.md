---
title: bag_remove_keys () — обозреватель данных Azure
description: В этой статье описывается bag_remove_keys () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/04/2020
ms.openlocfilehash: fffbda419ac123af8e2744b76c7acbe560c07ce9
ms.sourcegitcommit: 2764e739b4ad51398f4f0d3a9742d7168c4f5fd7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712353"
---
# <a name="bag_remove_keys"></a>bag_remove_keys ()

Удаляет ключи и связанные значения из `dynamic` контейнера свойств.

## <a name="syntax"></a>Синтаксис

`bag_remove_keys(`*контейнер* `, ` *ключи*`)`

## <a name="arguments"></a>Аргументы

* *контейнер*: `dynamic` входные данные контейнера свойств.
* *ключи*: `dynamic` массив включает ключи, которые будут удалены из входных данных. Ключи ссылаются на первый уровень контейнера свойств.
Указание ключей на вложенных уровнях не поддерживается.

## <a name="returns"></a>Возвращает

Возвращает `dynamic` контейнер свойств без указанных ключей и их значений.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(input:dynamic)
[
    dynamic({'key1' : 123,     'key2': 'abc'}),
    dynamic({'key1' : 'value', 'key3': 42.0}),
]
| extend result=bag_remove_keys(input, dynamic(['key2', 'key4']))
```

|input|набор по|
|---|---|
|{<br>  "key1": 123,<br>  "key2": "ABC"<br>}|{<br>  "key1": 123<br>}|
|{<br>  "key1": "значение",<br>  "Key3": 42,0<br>}|{<br>  "key1": "значение",<br>  "Key3": 42,0<br>}|
