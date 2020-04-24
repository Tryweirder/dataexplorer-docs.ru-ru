---
title: strcat () - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описана strcat() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dd01f875b45be038371cc184987aa2a8f8b8d5eb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506925"
---
# <a name="strcat"></a>strcat()

Конкатетирует от 1 до 64 аргументов.

* В случае, если аргументы не имеют типа строки, они будут принудительно преобразованы в строку.

**Синтаксис**

`strcat(`*argument1*,*argument2* , *argumentN*`)`

**Аргументы**

* *argument1* ... *argumentN* : выражения, которые должны быть сдуманы.

**Возвращает**

Аргументы, скатированные в одну строку.

**Примеры**
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|Здравствуй, мир!|