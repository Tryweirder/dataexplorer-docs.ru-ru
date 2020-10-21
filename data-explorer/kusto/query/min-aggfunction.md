---
title: min () (агрегатная функция) — обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается min () (агрегатная функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/24/2019
ms.openlocfilehash: c6cd5b219b1f89e68d34b37b21135e5ec02d6ee8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248930"
---
# <a name="min-aggregation-function"></a>min () (агрегатная функция)

Возвращает минимальное значение в группе. 

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

`summarize``min(` *Выражение*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. 

## <a name="returns"></a>Результаты

Минимальное значение *выражения* в группе.
 
> [!TIP]
> Это дает минимальный или максимальный собственный, например, самую низкую или наименьшую цену. Но если вам нужны другие столбцы в строке, например, имя поставщика с наименьшей ценой — используйте [arg_max](arg-max-aggfunction.md) или [arg_min](arg-min-aggfunction.md).