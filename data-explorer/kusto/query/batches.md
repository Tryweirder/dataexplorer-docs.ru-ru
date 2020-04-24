---
title: Пакеты - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описаны пакеты в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd695a1e7ffd9980de2750d38ad9538eeb877538
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518026"
---
# <a name="batches"></a>Пакеты

Запрос может включать несколько табликных высыльных инструкций,`;`если они делимитированы запятой () символом. Затем запрос возвращает несколько табликовых результатов, полученных в виде табликовых выражений, и упорядочен в соответствии с порядком инструкций в тексте запроса.

Например, следующий запрос дает два табликовских результата. Инструменты агента пользователя могут отображать эти результаты`Count of events in Florida` с `Count of events in Guam`соответствующим именем, связанным с каждым из них (и, соответственно).

```kusto
StormEvents | where State == "FLORIDA" | count | as ['Count of events in Florida'];
StormEvents | where State == "GUAM" | count | as ['Count of events in Guam']
```

Пакет особенно полезен для сценариев, в которых существует общий расчет, который используется несколькими подзапросами, например для панелей мониторинга. Если общий расчет является сложным, рекомендуется построить запрос таким образом, чтобы он был выполнен только один раз, используя [функцию материализоваться()](./materializefunction.md):

```kusto
let m = materialize(StormEvents | summarize n=count() by State);
m | where n > 2000;
m | where n < 10
```

Примечания.
* Предпочитают пакетирование и [`materialize`](materializefunction.md) более с помощью [вилки оператора](forkoperator.md).