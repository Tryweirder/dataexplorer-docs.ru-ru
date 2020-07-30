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
ms.openlocfilehash: 2568196dc20042e95521ed0818bd625f3394599b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342569"
---
# <a name="strcat_delim"></a>strcat_delim()

Выполняет сцепление между 2 и 64 аргументами с разделителем, указанным в качестве первого аргумента.

 * Если аргументы не принадлежат строковому типу, они будут принудительно преобразованы в строку.

## <a name="syntax"></a>Синтаксис

`strcat_delim(`*Разделитель*, *argument1*, *argument2*[, *аргументн*]`)`

## <a name="arguments"></a>Аргументы

* *Разделитель*: строковое выражение, которое будет использоваться в качестве разделителя.
* *argument1* ... *аргументн*: объединяемые выражения.

## <a name="returns"></a>Результаты

Аргументы, Объединенные в одну строку с *разделителем*.

## <a name="examples"></a>Примеры

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|
