---
title: Заявление Alias - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается заявление Alias в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c6c689ab6daacebe1cd20742b199c8b9cc299245
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766089"
---
# <a name="alias-statement"></a>Инструкция alias

::: zone pivot="azuredataexplorer"

Заявления псевдонимов позволяют определить псевдоним для баз данных, которые могут быть использованы позже в том же запросе.

Это полезно при работе с несколькими кластерами при попытке отобраться как работа на меньшем меньшем емких кластерах или только в одном кластере.
Псевдоним должен быть определен в соответствии со следующим синтаксисом, где *название кластера* и *название базы данных* должны быть существующими и действительными сущностями.

**Синтаксис**

`alias`база данных *'DatabaseAliasName'*- `=` кластер ("https://*clustername*.kusto.windows.net:443").*databasename*

`alias`База данных *DatabaseAliasName* `=` кластера ("https://*clustername*.kusto.windows.net:443").*databasename*

* *'DatabaseAliasName'* может быть либо en axisting имя или новое имя.
* Отображеное кластер-ури и отображеное имя базы данных должны отображаться внутри двойных котировок () или однократных (')

**Примеры**

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

Это не поддерживается в Azure Monitor

::: zone-end
