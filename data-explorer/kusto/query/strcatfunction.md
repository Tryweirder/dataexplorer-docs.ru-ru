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
ms.openlocfilehash: af25bb0407c9bc0c004c2f22e326ac034c6682cd
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717110"
---
# <a name="strcat"></a>strcat()

Объединяет аргументы от 1 до 64.

* Если аргументы не принадлежат строковому типу, они будут принудительно преобразованы в строку.

**Синтаксис**

`strcat(`*argument1*, *Argument2*[, *аргументн*]`)`

**Аргументы**

* *argument1* ... *аргументн*: объединяемые выражения.

**Возвращает**

Аргументы, Объединенные в одну строку.

**Примеры**
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|Здравствуй, мир!|
