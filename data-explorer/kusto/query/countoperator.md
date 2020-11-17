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
ms.openlocfilehash: 14895e17d77e3bbf1d98d2d68221930d3f291774
ms.sourcegitcommit: f7bebd245081a5cdc08e88fa4f9a769c18e13e5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94644626"
---
# <a name="count-operator"></a>Оператор count

Возвращает количество записей во входном наборе записей.

## <a name="syntax"></a>Синтаксис

`T | count`

## <a name="arguments"></a>Аргументы

*T*— табличные данные, записи которых будут подсчитаны.

## <a name="returns"></a>Возвращаемое значение

Эта функция возвращает таблицу с одной записью и столбцом типа `long`. Значение единственной ячейки — количество записей в *T*. 

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

## <a name="see-also"></a>См. также раздел

Дополнительные сведения о статистической функции Count () см. в разделе [Count () (агрегатная функция)](count-aggfunction.md).
