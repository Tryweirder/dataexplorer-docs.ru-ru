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
ms.openlocfilehash: 7454bd86a6ca2a835dc0515a9c8901a444259f12
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294531"
---
# <a name="drop-ingestion-mapping"></a>.create ingestion mapping

Удаляет сопоставление приема из базы данных.
 
`.drop``table` *TableName* `ingestion` *маппингкинд* `mapping` *MappingName*   

**Пример** 

```kusto
.drop table MyTable ingestion CSV mapping "Mapping1" 

.drop table MyTable ingestion json mapping "Mapping1" 
```
