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
ms.openlocfilehash: fb209ff3784f6e24f184b576a1e8f94c52834ba4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340961"
---
# <a name="tolong"></a>tolong()

Преобразует входные данные в длинное (64-разрядное) числовое представление чисел.

```kusto
tolong("123") == 123
```

## <a name="syntax"></a>Синтаксис

`tolong(`*Выражения*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет преобразовано в Long. 

## <a name="returns"></a>Результаты

Если преобразование выполнено успешно, результатом будет целое число.
Если преобразование не выполнено, результатом будет `null` .
 
*Примечание*. рекомендуется использовать [Long ()](./scalar-data-types/long.md) , если это возможно.