---
title: tdigest_merge() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описана tdigest_merge (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 0b7de916dd53c19a49301c8048e2d8867d1b1249
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506398"
---
# <a name="tdigest_merge-aggregation-function"></a>tdigest_merge() (функция агрегирования)

Слияния tdigest результаты по всей группе. 

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md).

Узнайте больше о базовом алгоритме (T-Digest) и предполагаемой ошибке [здесь](percentiles-aggfunction.md#estimation-error-in-percentiles).

**Синтаксис**

резюмировать `tdigest_merge(` *Expr*`)`.

резюмировать `tdigest_merge(` *Expr* `)` - псевдоним.

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации. 

**Возвращает**

Слияние значений tdigest *Expr* по всей группе.
 

**Советы**

1) Вы можете использовать`percentile_tdigest()`эту функцию (percentile-tdigestfunction.md).

2) Все tdigests, которые включены в ту же группу должны быть одного и того же типа.