---
title: тодеЦимал () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается тодеЦимал () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d4b9f020a1d37b7279c0712951ed0c2e39e9e428
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250500"
---
# <a name="todecimal"></a>todecimal()

Преобразует входные данные в десятичное число.

```kusto
todecimal("123.45678") == decimal(123.45678)
```

## <a name="syntax"></a>Синтаксис

`todecimal(`*Выражения*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет преобразовано в десятичное. 

## <a name="returns"></a>Результаты

Если преобразование выполнено успешно, результатом будет десятичное число.
Если преобразование не выполнено, результатом будет `null` .
 
*Примечание*. по возможности рекомендуется использовать [Real ()](./scalar-data-types/real.md) .