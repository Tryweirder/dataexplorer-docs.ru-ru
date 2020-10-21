---
title: толонг () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается толонг () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bf4960ae3bd11697e4e7203438e1a33af6c90672
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92240997"
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

## <a name="returns"></a>Результаты

Если преобразование выполнено успешно, результатом будет целое число.
Если преобразование не выполнено, результатом будет `null` .
 