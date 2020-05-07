---
title: Набор результатов запросов превысил внутренний ... лимит - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается набор результатов запроса превысил внутренний ... ограничение в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: efb07e68922a88e2cf59ef1eefeabd3af085aed4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523024"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>Набор результатов запросов превысил внутренний ... Предел

*Набор результатов запроса превысил внутренний ... ограничение* — это своего рода [частичный сбой запроса,](partialqueryfailures.md) который происходит, когда результат запроса превысил один из двух пределов:
* Ограничение на количество записей`record count limit`(установленное по умолчанию до 500 000).
* Ограничение на общий объем`data size limit`данных (, установленный по умолчанию до 67 108 864 (64 МБ)). 

Есть несколько возможных курсов действий, когда это происходит:
* Измените запрос, чтобы потреблять меньше ресурсов. Например, можно попытаться ограничить количество записей, возвращенных запросом (с помощью [оператора take](../query/takeoperator.md) или путем добавления дополнительных, [где оговорки),](../query/whereoperator.md)или уменьшить количество столбцов, возвращенных запросом (с помощью [оператора проекта](../query/projectoperator.md) или [оператора проекта),](../query/projectawayoperator.md)или использовать [обобщение оператора](../query/summarizeoperator.md) для получения агрегированных данных и т.д.
* Временно увеличьте ограничение соответствующего запроса для этого запроса **(см. Усечение результата** в [пределах запроса).](querylimits.md)  
  Обратите внимание, что это не рекомендуется в целом, так как существуют ограничения для защиты кластера специально, чтобы убедиться, что один запрос не нарушает одновременные запросы, работающие на кластере.