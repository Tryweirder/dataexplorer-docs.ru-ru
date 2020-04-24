---
title: current_cluster_endpoint () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны current_cluster_endpoint () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 65ce130b4dd3e0a3125eefc6c410775647f9b964
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516853"
---
# <a name="current_cluster_endpoint"></a>current_cluster_endpoint()

Возвращает конечную точку сети (имя DNS) запрашиваемого текущего кластера.

**Синтаксис**

`current_cluster_endpoint()`

**Возвращает**

Конечная точка сети (dNS-имя) запрашиваемого текущего кластера как значение типа. `string`

**Пример**

```kusto
print strcat("This query executed on: ", current_cluster_endpoint())
```