---
title: Max () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются функции Max () (агрегатная функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4d31f2137fcd64deab713522b1596f4c572606cc
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346853"
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