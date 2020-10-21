---
title: Оператор Alias — Azure обозреватель данных
description: В этой статье описывается инструкция Alias в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 822c8eccf50dc30fd3f56f4402c10a9fafb34084
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248298"
---
# <a name="alias-statement"></a>Инструкция alias

::: zone pivot="azuredataexplorer"

Операторы Alias позволяют определить псевдоним для баз данных, который можно использовать позже в том же запросе.

Это полезно при работе с несколькими кластерами, но при работе с меньшим количеством кластеров необходимо иметь значение.
Псевдоним должен быть определен в соответствии со следующим синтаксисом, где *имя_кластера* и *DatabaseName* являются существующими и допустимыми сущностями.

## <a name="syntax"></a>Синтаксис

`alias` база данных [*"датабасеалиаснаме"*] `=` кластер ("https://*имя_кластера*. kusto. Windows. NET: 443"). база данных ("*DatabaseName*")

`alias` кластер *датабасеалиаснаме* базы данных `=` ("https://*имя_кластера*. kusto. Windows. NET: 443"). база данных ("*DatabaseName*")

* *"Датабасеалиаснаме"* может быть либо существующим именем, либо новым именем.
* Сопоставленный кластер-URI и имя сопоставленной базы данных должны находиться в двойных кавычках (") или одинарных кавычках (')

## <a name="examples"></a>Примеры

```kusto
alias database["wiki"] = cluster("https://somecluster.kusto.windows.net:443").database("somedatabase");
database("wiki").PageViews | count 
```

```kusto
alias database Logs = cluster("https://othercluster.kusto.windows.net:443").database("otherdatabase");
database("Logs").Traces | count 
```

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
