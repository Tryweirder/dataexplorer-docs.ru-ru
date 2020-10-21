---
title: AVG () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается AVG () (агрегатная функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 949bd463f57b9eb0b674fe780aa50e78e30926a2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248036"
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
 