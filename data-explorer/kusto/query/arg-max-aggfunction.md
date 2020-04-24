---
title: arg_max() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается arg_max (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 73953a17b1819081c8458d5dbde3fa6d55c8866e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518961"
---
# <a name="arg_max-aggregation-function"></a>arg_max() (функция агрегирования)

Находит строку в группе, которая максимизирует *ExprToMaximize*и возвращает `*` значение *ExprToReturn* (или вернуть всю строку).

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

`summarize``(`*NameExprToMaximize* `,` *NameExprToReturn* `,` `)=` `arg_max` `(` *ExprToMaximize*, `*`  |  *ExprToReturn* ...`,``)`

**Аргументы**

* *ExprToMaximize :* Выражение, которое будет использоваться для расчета агрегации. 
* *ExprToReturn*: Выражение, которое будет использоваться для возвращения значения, когда *ExprToMaximize* является максимальным. Выражение возврата может быть подстановочным знаком (кв.) для возврата всех столбцов таблицы ввода.
* *NameExprToMaximize :* Дополнительное имя для столбца результата, представляющего *ExprToMaximize*.
* *NameExprToReturn*: Дополнительные дополнительные имена для столбцов результатов, представляющих *ExprToReturn*.

**Возвращает**

Находит строку в группе, которая максимизирует *ExprToMaximize*и возвращает `*` значение *ExprToReturn* (или вернуть всю строку).

**Примеры**

Смотрите примеры функции агрегирования [arg_min ()](arg-min-aggfunction.md)