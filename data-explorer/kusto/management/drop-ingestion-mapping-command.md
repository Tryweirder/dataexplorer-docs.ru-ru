---
title: отображение ingestion .drop - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается отображение ingestion .drop в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: c1434234033acc73de35289c6bc0a90af727babb
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744761"
---
# <a name="drop-ingestion-mapping"></a>.create ingestion mapping

Выбрасывает отображение по навозу из базы данных.
 
`.drop``table` *TableName* `ingestion` *КартированиеKind* `mapping` *MappingName*   

**Пример** 

```kusto
.drop table MyTable ingestion CSV mapping "Mapping1" 

.drop table MyTable ingestion JSON mappings "Mapping1" 
```
