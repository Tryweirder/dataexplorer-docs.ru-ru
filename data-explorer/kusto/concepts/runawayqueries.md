---
title: Беглые запросы - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны запросы Runaway в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 076afea64cf3c4405f37e9e4014584b90d58ca07
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522990"
---
# <a name="runaway-queries"></a>Неконтролируемые запросы

*Беглый запрос* — это своего рода [частичный сбой запроса,](partialqueryfailures.md) который происходит при превышении некоего [внутреннего лимита запроса](querylimits.md) при выполнении запроса.

Например, можно сообщить следующую ошибку:`HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete.`

Есть несколько возможных курсов действий, когда это происходит:
* Измените запрос, чтобы потреблять меньше ресурсов. Например, если ошибка указывает на то, что набор результатов запроса слишком велик, можно попытаться ограничить количество записей, возвращенных запросом (с помощью [оператора take](../query/takeoperator.md) или путем добавления дополнительных, [где оговорки),](../query/whereoperator.md)или уменьшить количество столбцов, возвращенных запросом (с помощью оператора [проекта](../query/projectoperator.md) или [оператора проекта),](../query/projectawayoperator.md)или использовать [обобщенные оператор](../query/summarizeoperator.md) для получения агрегированных данных и т.д.
* Временно увеличьте ограничение соответствующего запроса для этого запроса (см. **память Max на итератор результата** под [ограничениями запроса).](querylimits.md)  
  Обратите внимание, что это не рекомендуется в целом, так как существуют ограничения для защиты кластера специально, чтобы убедиться, что один запрос не нарушает одновременные запросы, работающие на кластере.
  
  
  