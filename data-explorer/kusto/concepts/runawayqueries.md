---
title: Неконтролируемые запросы — Azure обозреватель данных
description: В этой статье описаны неконтролируемые запросы в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4f2cf559863eecbd267855cd4a22f2dbe8673dc7
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356611"
---
# <a name="runaway-queries"></a>Неконтролируемые запросы

*Неконтролируемый запрос* — это разновидность [сбоя частичного запроса](partialqueryfailures.md) , которая происходит при превышении [предела внутренних запросов](querylimits.md) во время выполнения запроса. 

Например, может появиться следующее сообщение об ошибке: `HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete.`

Существует несколько возможных курсов действий.
* Измените запрос, чтобы он использовал меньше ресурсов. Например, если ошибка указывает, что результирующий набор запроса слишком большой, можно:
  * Ограничьте число записей, возвращаемых запросом
     * Использование [оператора take](../query/takeoperator.md)
     * Добавление дополнительных [предложений WHERE](../query/whereoperator.md)
  * Сократите число столбцов, возвращаемых запросом.
     * Использование [оператора Project](../query/projectoperator.md)
     * Использование [оператора размещения по проектам](../query/projectawayoperator.md)
     * Использование [оператора сохранения проекта](../query/project-keep-operator.md)
  * Для получения агрегированных данных используйте [оператор суммирования](../query/summarizeoperator.md) .
* Временно увеличьте количество соответствующих запросов для этого запроса. Дополнительные сведения см. в разделе [ограничения запросов — ограничение памяти для каждого итератора](querylimits.md). Однако этот метод не рекомендуется. Существуют ограничения для защиты кластера и обеспечения того, что один запрос не нарушает параллельные запросы, выполняющиеся в кластере.
