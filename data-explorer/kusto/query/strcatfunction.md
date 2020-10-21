---
title: strcat () — Azure обозреватель данных
description: В этой статье описывается strcat () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 55a52ff4aece3fa1a3197db0fb47984217292136
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251526"
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
