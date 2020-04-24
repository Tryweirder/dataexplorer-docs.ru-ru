---
title: strcat_array() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны strcat_array () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d2412762cf68243e3952a8ad12a5b919d947bd3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506959"
---
# <a name="strcat_array"></a>strcat_array()

Создает скатную строку значений массива с помощью заданного делимитера.
    
**Синтаксис**

`strcat_array(`*массив,* *делимитер*`)`

**Аргументы**

* *массив*: `dynamic` Значение, представляющее массив значений, которые должны быть совмещены.
* *delimeter*: `string` Значение, которое будет использоваться для совмещения значений в *массиве*

**Возвращает**

Значения массива, скатированные в одну строку.

**Примеры**
  
```kusto
print str = strcat_array(dynamic([1, 2, 3]), "->")
```

|str|
|---|
|1->2->3|