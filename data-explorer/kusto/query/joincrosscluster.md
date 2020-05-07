---
title: Соединение между кластерами с обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается соединение между кластерами в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: bd2ebaa35de1997a96c6646c0fe0f7e248af2240
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737323"
---
# <a name="cross-cluster-join"></a>Соединение между кластерами

::: zone pivot="azuredataexplorer"

Общие сведения о запросах между кластерами см. в разделе [запросы между кластерами и между базами данных](cross-cluster-or-database-queries.md)

Можно выполнить операцию JOIN с наборами данных, находящимися в разных кластерах. Например. 

```kusto
T | ... | join (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1 // (1)

cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster2").database("SomeDB2").T2 | ...) on Col1 // (2)
```

В примерах, приведенных выше, операция JOIN является межкластерным соединением, предполагая, что текущий кластер не является ни "Сомеклустер", ни "SomeCluster2".

Обратите внимание, что в следующем примере

```kusto
cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster").database("SomeDB2").T2 | ...) on Col1 
```

операция JOIN не является соединением между кластерами, так как оба операнда происходят в одном кластере.

Когда Kusto встречает соединение между кластерами, он автоматически решает, где выполнить операцию JOIN. Это решение может иметь один из трех возможных результатов:
* Выполнить операцию JOIN в кластере левого операнда, правый операнд будет сначала выбран этим кластером. (соединение в примере **(1)** будет выполнено в локальном кластере).
* Выполнить операцию JOIN в кластере правого операнда, левый операнд будет сначала выбран этим кластером. (JOIN в примере **(2)** будет выполнен в "SomeCluster2")
* Выполнить операцию Join локально (т. е. в кластере, который получил запрос), оба операнда будут сначала получены локальным кластером.

Фактическое решение зависит от конкретного запроса, стратегии удаленного взаимодействия с автоматическим присоединением (упрощенная версия): если один из операндов является локальным, соединение будет выполняться локально. Если оба операнда являются удаленным соединением, то в кластере правого операнда будет выполняться.

Иногда производительность запроса может быть значительно выше, если не соблюдается стратегия автоматического удаленного взаимодействия. Как правило, для выполнения операции JOIN в кластере наибольшего операнда лучше всего (с точки зрения производительности).

Если в примере **(1)** набор данных, созданный ```T | ...``` с помощью, гораздо меньше, чем ```cluster("SomeCluster").database("SomeDB").T2 | ...``` один из созданных, более эффективным будет выполнение операции JOIN с "сомеклустер".

Это можно сделать, предоставив указание Kusto Join Remoting. Синтаксис:

```kusto
T | ... | join hint.remote=<strategy> (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1
```

Ниже приведены допустимые значения для*`strategy`*
* **`left`**— выполнение объединения в кластере левого операнда 
* **`right`**— выполнение объединения в кластере правого операнда;
* **`local`**— выполнить присоединение в кластере текущего кластера;
* **`auto`**-(по умолчанию). Позвольте Kusto сделать решение об автоматическом удаленном взаимодействии

**Примечание.** Указание присоединение к удаленному взаимодействию будет игнорироваться Kusto, если указанная стратегия не применима к операции JOIN.

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end