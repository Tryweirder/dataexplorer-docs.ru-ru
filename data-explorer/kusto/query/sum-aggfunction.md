---
title: сумма () (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается сумма () (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b729d9be8aba9683a053ef80acb3936c0c64d6a8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506687"
---
# <a name="sum-aggregation-function"></a>сумма() (функция агрегирования)

Рассчитывает сумму *Expr* по всей группе. 

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

резюмировать `sum(` *Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации. 

**Возвращает**

Сумма значение *Expr* по всей группе.
 