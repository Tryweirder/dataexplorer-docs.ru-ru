---
title: current_cluster_endpoint () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается current_cluster_endpoint () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cfd35837b0c1affbb2101e6c71a4fa638ef8e466
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252555"
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