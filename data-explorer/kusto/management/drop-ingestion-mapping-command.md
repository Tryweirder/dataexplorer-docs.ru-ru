---
title: . Удаление сопоставления приема в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается удаление сопоставления приема в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 69ab4849d67d7cc7507bab075b0111fbd8ec95e7
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780615"
---
# <a name="drop-ingestion-mapping"></a>.create ingestion mapping

Удаляет сопоставление приема из базы данных.
 
`.drop``table` *TableName* `ingestion` *маппингкинд* `mapping` *MappingName*   

**Пример** 

```kusto
.drop table MyTable ingestion csv mapping "Mapping1" 

.drop table MyTable ingestion json mapping "Mapping1" 
```
