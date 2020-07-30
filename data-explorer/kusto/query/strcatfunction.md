---
title: strcat () — Azure обозреватель данных
description: В этой статье описывается strcat () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1f26b4bf267a4387748fe4c4c26636579607de51
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351001"
---
# <a name="strcat"></a>strcat()

Объединяет аргументы от 1 до 64.

* Если аргументы не принадлежат строковому типу, они будут принудительно преобразованы в строку.

## <a name="syntax"></a>Синтаксис

`strcat(`*argument1*, *Argument2*[, *аргументн*]`)`

## <a name="arguments"></a>Аргументы

* *argument1* ... *аргументн*: объединяемые выражения.

## <a name="returns"></a>Результаты

Аргументы, Объединенные в одну строку.

## <a name="examples"></a>Примеры
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|Здравствуй, мир!|
