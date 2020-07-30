---
title: ToDouble ()/тореал () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается ToDouble ()/тореал () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 34734f3975b1720c1d009f190d4fae2ebc54283f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350763"
---
# <a name="todouble-toreal"></a>todouble(), toreal()

Преобразует входные данные в значение типа `real` . ( `todouble()` и `toreal()` являются синонимами.)

```kusto
toreal("123.4") == 123.4
```

## <a name="syntax"></a>Синтаксис

`toreal(`*Expr* `)` 
 Выражение `todouble(` *Выражение*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, значение которого будет преобразовано в значение типа `real` .

## <a name="returns"></a>Результаты

Если преобразование выполнено успешно, результатом является значение типа `real` .
Если преобразование не выполнено, результатом является значение `real(null)` .

*Примечание*. предпочитать использование [Double () или Real ()](./scalar-data-types/real.md) , если это возможно.