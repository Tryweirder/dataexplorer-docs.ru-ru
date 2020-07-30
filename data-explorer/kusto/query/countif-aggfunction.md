---
title: СЧЁТЕСЛИ () (агрегатная функция) — обозреватель данных Azure | Документация Майкрософт
description: В этой статье описываются функции СЧЁТЕСЛИ () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a5b69b50c0cf4c07934d7900937675bf6338eab9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348774"
---
# <a name="countif-aggregation-function"></a>СЧЁТЕСЛИ () (агрегатная функция)

Возвращает число строк, для которых предикат *Predicate* принимает значение `true`.

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

См. также функция- [Count ()](count-aggfunction.md) , которая подсчитывает строки без выражения предиката.

## <a name="syntax"></a>Синтаксис

Итоговый `countif(` *предикат*`)`

## <a name="arguments"></a>Аргументы

* *Predicate*: выражение, которое будет использоваться для вычисления агрегата. 

## <a name="returns"></a>Результаты

Возвращает число строк, для которых предикат *Predicate* принимает значение `true`.

> [!TIP]
> Используйте `summarize countif(filter)` вместо `where filter | summarize count()`