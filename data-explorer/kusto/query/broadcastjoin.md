---
title: Широковещательное соединение — обозреватель данных Azure
description: В этой статье описывается широковещательное соединение в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7987f44437d673b90954b674e63b70ac35e2432c
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128688"
---
# <a name="broadcast-join"></a>Широковещательное соединение

Сейчас регулярные объединения выполняются на одном узле кластера.
Широковещательное соединение — это стратегия выполнения объединения, которая распределяет его по узлам кластера. Эта стратегия полезна, если левая часть объединения является небольшой (до 100 КБ). В этом случае широковещательное соединение будет более производительным, чем обычное соединение.

Если левая часть объединения является небольшим набором данных, вы можете выполнить присоединение в широковещательном режиме, используя следующий синтаксис (Подсказка. Стратегия = широковещательная):

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on key
```

Повышение производительности будет более заметным в тех случаях, когда за соединение следуют другие операторы, такие как `summarize` . Например, в этом запросе:

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on Key
| summarize dcount(Messages) by Timestamp, Key
```