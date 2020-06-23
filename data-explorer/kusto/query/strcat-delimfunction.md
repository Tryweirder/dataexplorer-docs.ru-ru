---
title: strcat_delim () — обозреватель данных Azure
description: В этой статье описывается strcat_delim () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f6a78a5abb92aa93fe8b1ae15ea8968f71bde07c
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264561"
---
# <a name="strcat_delim"></a>strcat_delim()

Выполняет сцепление между 2 и 64 аргументами с разделителем, указанным в качестве первого аргумента.

 * Если аргументы не принадлежат строковому типу, они будут принудительно преобразованы в строку.

**Синтаксис**

`strcat_delim(`*Разделитель*, *argument1*, *argument2*[, *аргументн*]`)`

**Аргументы**

* *Разделитель*: строковое выражение, которое будет использоваться в качестве разделителя.
* *argument1* ... *аргументн*: объединяемые выражения.

**Возвращает**

Аргументы, Объединенные в одну строку с *разделителем*.

**Примеры**

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|
