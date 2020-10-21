---
title: Пакеты Azure обозреватель данных
description: В этой статье описываются пакеты в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: efd4b4c5387016bd66027bcf5e0722f8039f0837
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252633"
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
