---
title: Соединение трансляции - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается соединение вещания в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 72c89bf2160f8f5b735fd8c93f9519feae9114d9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517312"
---
# <a name="broadcast-join"></a>Трансляция Присоединиться

Сегодня регулярные соединения выполняются на одном кластерном узлах.
Соединение вещания — это стратегия выполнения соединения, которая будет распределять его по кластерным узлам, и это полезно, когда левая сторона соединения мала (до 100K записей), В этом случае, соединение будет более выполнять, чем обычное соединение.

Если левая сторона соединения представляет собой небольшой набор данных, то вы можете запустить соединение в режиме вещания, используя следующий синтаксис (hint.strategy - трансляция):

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on key
```

Улучшение производительности будет более заметным в сценариях, где `summarize`за соединением следуют другие операторы, такие как . например, в этом запросе:

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on Key
| summarize dcount(Messages) by Timestamp, Key
```