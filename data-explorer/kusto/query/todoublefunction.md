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
ms.openlocfilehash: 8e93e86814adf2789d01e03173196468f085b7c2
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804106"
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

## <a name="returns"></a>Возвращаемое значение

Если преобразование выполнено успешно, результатом является значение типа `real` .
Если преобразование не выполнено, результатом является значение `real(null)` .
