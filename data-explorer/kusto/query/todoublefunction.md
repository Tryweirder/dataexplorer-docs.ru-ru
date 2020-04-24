---
title: удвоить()/тореальную() - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается дубль()/тореальный() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: eb9c976f1646f71fcf8b345899037461f58f4ef0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506330"
---
# <a name="todoubletoreal"></a>удвоить()/тореальную()

Преобразует входные значения в значение `real`типа. `todouble()` (и `toreal()` являются синонимами.)

```kusto
toreal("123.4") == 123.4
```

**Синтаксис**

`toreal(`*Expr*`)`
`todouble(`*Expr*`)`

**Аргументы**

* *Expr*: Выражение, значение которого будет `real`преобразовано в значение типа.

**Возвращает**

Если преобразование успешно, результатом `real`является значение типа .
Если преобразование не является успешным, результатом является значение `real(null)`.

*Примечание*: Предпочитаюиспользовать использование [двойной () или реальной ()](./scalar-data-types/real.md) когда это возможно.