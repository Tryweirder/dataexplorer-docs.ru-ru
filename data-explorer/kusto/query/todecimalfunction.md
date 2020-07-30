---
title: тодеЦимал () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается тодеЦимал () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f699508436c9e2533661a440be2ac8f5f8d94688
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350780"
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