---
title: толонг () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается толонг () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 36c0317f046f146d2812b8830d7fe571d5363c59
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804123"
---
# <a name="tolong"></a>tolong()

Преобразует входные данные в длинное (64-разрядное) числовое представление чисел.

```kusto
tolong("123") == 123
```

> [!NOTE]
> Предпочитать использование [Long ()](./scalar-data-types/long.md) , если это возможно.

## <a name="syntax"></a>Синтаксис

`tolong(`*Выражения*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет преобразовано в Long. 

## <a name="returns"></a>Возвращаемое значение

Если преобразование выполнено успешно, результатом будет целое число.
Если преобразование не выполнено, результатом будет `null` .
 