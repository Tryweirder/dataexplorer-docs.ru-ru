---
title: strcat_delim () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описывается strcat_delim () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f944af741cd5f655c2c9b090ddebc6cc35a47766
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506942"
---
# <a name="strcat_delim"></a>strcat_delim()

Concatenates между 2 и 64 аргументы, с делимитетом, при условии, в качестве первого аргумента.

 * В случае, если аргументы не имеют типа строки, они будут принудительно преобразованы в строку.

**Синтаксис**

`strcat_delim(`*делимитер*,*argument1*,*argument2* , *argumentN*`)`

**Аргументы**

* *делимитер*: выражение строки, которое будет использоваться в качестве сепаратора.
* *argument1* ... *argumentN* : выражения, которые должны быть сдуманы.

**Возвращает**

Аргументы, скатированные в одну строку с *делимитетом.*

**Примеры**

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|