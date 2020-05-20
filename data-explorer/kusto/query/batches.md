---
title: Пакеты Azure обозреватель данных
description: В этой статье описываются пакеты в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e5a38d53fb9b28fc7da0ddf71132e3a047e8188e
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550339"
---
# <a name="batches"></a>Пакеты

Запрос может включать несколько операторов табличных выражений, если они разделяются символом точки с запятой ( `;` ). Затем запрос возвращает несколько табличных результатов. Результаты создаются инструкциями табличного выражения и упорядочиваются в соответствии с порядком инструкций в тексте запроса.

Например, следующий запрос выдает два табличных результата. После этого средства агента пользователя могут отобразить эти результаты с соответствующим именем, связанным с каждым ( `Count of events in Florida` и `Count of events in Guam` соответственно).

```kusto
StormEvents | where State == "FLORIDA" | count | as ['Count of events in Florida'];
StormEvents | where State == "GUAM" | count | as ['Count of events in Guam']
```

Пакетная обработка полезна в сценариях, где общее вычисление совместно используется несколькими вложенными запросами, например для панелей мониторинга. Если общее вычисление является сложным, используйте [функцию материализации ()](./materializefunction.md) и создайте запрос, чтобы он выполнялся только один раз:

```kusto
let m = materialize(StormEvents | summarize n=count() by State);
m | where n > 2000;
m | where n < 10
```

Примечания.
* Предпочитать пакетную обработку и [`materialize`](materializefunction.md) использование [оператора ветвления](forkoperator.md).
