---
title: strcat_delim () — обозреватель данных Azure
description: В этой статье описывается strcat_delim () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de34f4a2f6efb3af84b61f53da8d58cb60dea7ae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243704"
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
