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
ms.openlocfilehash: e62432773d99d74a46022cad3199f3bab0cae50b
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128477"
---
# <a name="todouble-toreal"></a>ToDouble (), тореал ()

Преобразует входные данные в значение типа `real` . ( `todouble()` и `toreal()` являются синонимами.)

```kusto
toreal("123.4") == 123.4
```

**Синтаксис**

`toreal(`*Expr* `)` 
 Выражение `todouble(` *Выражение*`)`

**Аргументы**

* *Expr*: выражение, значение которого будет преобразовано в значение типа `real` .

**Возвращает**

Если преобразование выполнено успешно, результатом является значение типа `real` .
Если преобразование не выполнено, результатом является значение `real(null)` .

*Примечание*. предпочитать использование [Double () или Real ()](./scalar-data-types/real.md) , если это возможно.