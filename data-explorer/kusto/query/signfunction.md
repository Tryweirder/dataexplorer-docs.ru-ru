---
title: Sign () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается знакомство () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8d6761cc2ffa9a8c28151c00720bbd1340a56875
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351086"
---
# <a name="sign"></a>sign()

Знак числового выражения

## <a name="syntax"></a>Синтаксис

`sign(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число.

## <a name="returns"></a>Результаты

* Положительный (+ 1), ноль (0) или отрицательный (-1) знак указанного выражения. 

## <a name="examples"></a>Примеры

```kusto
print s1 = sign(-42), s2 = sign(0), s3 = sign(11.2)

```

|s1|s2|s3|
|---|---|---|
|-1|0|1|