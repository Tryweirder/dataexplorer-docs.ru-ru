---
title: Широковещательное соединение — обозреватель данных Azure
description: В этой статье описывается широковещательное соединение в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0cbcd71cc4582a4f5dd966001ae32fd4cedf0c1e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245290"
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