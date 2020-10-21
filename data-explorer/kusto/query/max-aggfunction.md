---
title: Max () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются функции Max () (агрегатная функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: cd53f0701064d95ab2d088e774f49b06f8d323f7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251071"
---
# <a name="max-aggregation-function"></a>Max () (агрегатная функция)

Возвращает максимальное значение в группе. 

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

`summarize``max(` *Выражение*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. 

## <a name="returns"></a>Результаты

Максимальное значение *выражения* в группе.
 
> [!TIP]
> Это дает минимальный или максимальный собственный, например, самую низкую или наименьшую цену.
> Но если вам нужны другие столбцы в строке, например, имя поставщика с наименьшей ценой — используйте [arg_max](arg-max-aggfunction.md) или [arg_min](arg-min-aggfunction.md).