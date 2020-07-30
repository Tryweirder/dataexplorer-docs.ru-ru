---
title: тоинт () — Azure обозреватель данных
description: В этой статье описывается тоинт () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2daea4d190ed349c41a8eecf2eef53b2c2b93716
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350695"
---
# <a name="toint"></a>toint()

Преобразует входные данные в целочисленное (со знаком 32-разрядное) представление числа.

```kusto
toint("123") == int(123)
```

## <a name="syntax"></a>Синтаксис

`toint(`*Выражения*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет преобразовано в тип Integer. 

## <a name="returns"></a>Результаты

Если преобразование прошло успешно, результатом будет целое число.
Если преобразование завершилось неудачно, результатом будет `null` .
 
*Примечание*. предпочитать использование [int ()](./scalar-data-types/int.md) , если это возможно.
