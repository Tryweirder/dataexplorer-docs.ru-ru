---
title: Sum () (агрегатная функция) — обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается Sum () (агрегатная функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: cb2bb49a0580819a172657ba4258fad08457d1d7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251377"
---
# <a name="sum-aggregation-function"></a>Sum () (агрегатная функция)

Вычисляет сумму *выражения expr* по всей группе. 

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

суммировать `sum(` *выражение*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. 

## <a name="returns"></a>Результаты

Значение суммы *выражения expr* по группе.
 