---
title: ToDouble ()/тореал () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается ToDouble ()/тореал () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9a1a18ffdfc28d0487464202baa759acccd3e40e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250461"
---
# <a name="todouble-toreal"></a>todouble(), toreal()

Преобразует входные данные в значение типа `real` . ( `todouble()` и `toreal()` являются синонимами.)

```kusto
toreal("123.4") == 123.4
```

> [!NOTE]
> Предпочитать использование [Double () или Real ()](./scalar-data-types/real.md) , если это возможно.

## <a name="syntax"></a>Синтаксис

`toreal(`*Expr* `)` 
 Выражение `todouble(` *Выражение*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, значение которого будет преобразовано в значение типа `real` .

## <a name="returns"></a>Результаты

Если преобразование выполнено успешно, результатом является значение типа `real` .
Если преобразование не выполнено, результатом является значение `real(null)` .
