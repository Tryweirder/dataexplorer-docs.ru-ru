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
ms.openlocfilehash: e64413047ceb83860f7ea47a1540ae99faa7ec55
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863223"
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