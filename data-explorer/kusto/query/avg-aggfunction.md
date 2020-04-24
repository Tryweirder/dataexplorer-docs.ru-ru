---
title: avg() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описана функция авг () (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: aadc756bdf4c6cab805f58a8a600815cf29680f7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518349"
---
# <a name="avg-aggregation-function"></a>avg() (функция агрегирования)

Рассчитывает среднее значение *Expr* по всей группе. 

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

резюмировать `avg(` *Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации. Записи `null` со значениями игнорируются и не включаются в расчет.

**Возвращает**

Среднее значение *Expr* по всей группе.
 