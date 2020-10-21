---
title: Оператор Count — Azure обозреватель данных
description: В этой статье описывается оператор Count в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/16/2020
ms.openlocfilehash: bf595e27d7b4881dca7b5e2a370c90a8407a8c78
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252599"
---
# <a name="count-operator"></a>Оператор count

Возвращает количество записей во входном наборе записей.

## <a name="syntax"></a>Синтаксис

`T | count`

## <a name="arguments"></a>Аргументы

*T*— табличные данные, записи которых будут подсчитаны.

## <a name="returns"></a>Результаты

Эта функция возвращает таблицу с одной записью и столбцом типа `long`. Значение единственной ячейки — количество записей в *T*. 

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```
