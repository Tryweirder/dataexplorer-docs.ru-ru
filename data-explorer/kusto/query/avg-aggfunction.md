---
title: AVG () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается AVG () (агрегатная функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: f058af075a856d12a2a6a81419f32b6efbd9ea16
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349403"
---
# <a name="avg-aggregation-function"></a>AVG () (агрегатная функция)

Вычисляет среднее арифметическое для *выражения* в группе. 

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

суммировать `avg(` *выражение*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. Записи со `null` значениями игнорируются и не включаются в вычисление.

## <a name="returns"></a>Результаты

Среднее значение *выражения* в группе.
 