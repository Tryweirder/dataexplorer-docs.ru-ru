---
title: ToString () — Azure обозреватель данных
description: В этой статье описывается ToString () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2093ff1117cf7744af550cf93c3fe630fa40a6e6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340182"
---
# <a name="tostring"></a>tostring()

Преобразует входные данные в строковое представление.

```kusto
tostring(123) == "123"
```

## <a name="syntax"></a>Синтаксис

`tostring(`*`Expr`*`)`

## <a name="arguments"></a>Аргументы

* *`Expr`*: Выражение, которое будет преобразовано в строку. 

## <a name="returns"></a>Результаты

Если *`Expr`* значение не равно null, результатом будет строковое представление *`Expr`* .
Если *`Expr`* значение равно null, результатом будет пустая строка.
 