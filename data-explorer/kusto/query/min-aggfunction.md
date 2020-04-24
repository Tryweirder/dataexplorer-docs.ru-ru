---
title: мин() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается мин(функция агрегации) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/24/2019
ms.openlocfilehash: ca50210c84b39f19e6717b27089313d0d116e21a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512416"
---
# <a name="min-aggregation-function"></a>мин() (функция агрегирования)

Возвращает минимальное значение по всей группе. 

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

`summarize``min(` *Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации. 

**Возвращает**

Минимальное значение *Expr* по всей группе.
 
> [!TIP]
> Это дает вам мин или макс сам по себе - например, самая высокая или низкая цена. Но если вы хотите другие столбцы в строке - например, имя поставщика с самой низкой ценой - используйте [arg_max](arg-max-aggfunction.md) или [arg_min.](arg-min-aggfunction.md)