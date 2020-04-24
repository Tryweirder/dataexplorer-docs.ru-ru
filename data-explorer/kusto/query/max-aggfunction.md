---
title: max() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описано max() (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: bbfc9591fb20903d18486f9f249d3b1240f705e3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512569"
---
# <a name="max-aggregation-function"></a>max() (функция агрегирования)

Возвращает максимальное значение по всей группе. 

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

`summarize``max(` *Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации. 

**Возвращает**

Максимальное значение *Expr* по всей группе.
 
> [!TIP]
> Это дает вам мин или макс сам по себе - например, самая высокая или низкая цена.
> Но если вы хотите другие столбцы в строке - например, имя поставщика с самой низкой ценой - используйте [arg_max](arg-max-aggfunction.md) или [arg_min.](arg-min-aggfunction.md)