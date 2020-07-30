---
title: current_cluster_endpoint () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается current_cluster_endpoint () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2c3ddbee55e729ae8afbb6c1fbcc213bd6bfd9ce
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348723"
---
# <a name="current_cluster_endpoint"></a>current_cluster_endpoint()

Возвращает конечную точку сети (DNS-имя) текущего запрашиваемого кластера.

## <a name="syntax"></a>Синтаксис

`current_cluster_endpoint()`

## <a name="returns"></a>Результаты

Конечная точка сети (DNS-имя) текущего кластера, к которому выполняется запрос, в виде значения типа `string` .

## <a name="example"></a>Пример

```kusto
print strcat("This query executed on: ", current_cluster_endpoint())
```