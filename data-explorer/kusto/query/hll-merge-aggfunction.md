---
title: hll_merge() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается hll_merge (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 4700d5c87bf0f29f7bba86d56114a6a61092da94
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514116"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge() (функция агрегирования)

Слияние результатов HLL по всей группе в одно значение HLL.

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md).

Читайте о [базовом алгоритме (*H*yper*L*og*L*og) и точности оценки.](dcount-aggfunction.md#estimation-accuracy)

**Синтаксис**

`summarize``hll_merge(` *Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации. 

**Возвращает**

Слияние значений hll *Expr* по всей группе.
 
**Советы**

1) Вы можете использовать функцию «dcount_hll» (dcount-hllfunction.md), которая будет вычислять количество от функций агрегации hll/hll-merge.