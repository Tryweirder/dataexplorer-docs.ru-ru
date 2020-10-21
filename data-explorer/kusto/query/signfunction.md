---
title: Sign () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается знакомство () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: db6757e8c3ca871036cba1c21c1a20c3486b9249
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250011"
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