---
title: Результирующий набор запроса Kusto превышает внутреннее ограничение — Azure обозреватель данных
description: В этой статье описывается, что результирующий набор запросов превысил внутренний... ограничение в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 448da9db3e515385ca44cec94d9dd8604a8c1125
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356577"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>Результирующий набор запроса превысил внутренний... Размер

*Результирующий набор запроса превысил внутренний... ограничение* — это разновидность [сбоя частичного запроса](partialqueryfailures.md) , которая происходит, когда результат запроса превышает одно из двух ограничений:
* Ограничение числа записей (по `record count limit` умолчанию используется значение 500 000)
* Ограничение общего объема данных ( `data size limit` значение по умолчанию — 67 108 864 (64 МБ))

Существует несколько возможных курсов действий:

* Измените запрос, чтобы он использовал меньше ресурсов. 
  Например, администратор может сделать следующее:
  * Ограничьте число записей, возвращаемых запросом с помощью [оператора take](../query/takeoperator.md) или добавив дополнительные [предложения WHERE](../query/whereoperator.md) .
  * Попробуйте уменьшить количество столбцов, возвращаемых запросом. Использование [оператора проекта](../query/projectoperator.md), [оператора размещения](../query/projectawayoperator.md)в проекте или [оператора сохранения проекта](../query/project-keep-operator.md)
  * Использование [оператора суммирования](../query/summarizeoperator.md) для получения агрегированных данных
* Временно увеличьте количество соответствующих запросов для этого запроса. Дополнительные сведения см. в разделе **усечение результатов** в рамках [ограничений запросов](querylimits.md).

 > [!NOTE] 
 > Не рекомендуется увеличивать предельный объем запросов, так как существуют ограничения на защиту кластера. Ограничения позволяют убедиться, что один запрос не нарушает параллельные запросы, выполняющиеся в кластере.
  
