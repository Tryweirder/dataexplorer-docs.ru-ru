---
title: Команды управления политиками емкости в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются команды управления политиками емкости в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/02/2020
ms.openlocfilehash: 512ab14c2abc1f777376d81d4944caf2c3343513
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967338"
---
# <a name="capacity-policy-commands"></a>Команды политики емкости

Для управления [политикой емкости](../management/capacitypolicy.md)кластера можно использовать следующие команды управления.

Для команд требуются разрешения [аллдатабасесадмин](../management/access-control/role-based-authorization.md) .

## <a name="show-cluster-policy-capacity"></a>Отображение емкости политики кластера

```kusto
.show cluster policy capacity
```

Отображает текущую политику емкости для кластера.

**Выходные данные**

|Имя политики | Имя сущности | Политика | Дочерние сущности | Тип сущности
|---|---|---|---|---
|капаЦитиполици | | Строка в формате JSON, представляющая политику | Список баз данных в кластере |Кластер


## <a name="alter-cluster-policy-capacity"></a>изменение емкости политики кластера

```kusto
.alter cluster policy capacity @'{ ... capacity policy JSON representation ... }'
.alter-merge cluster policy capacity @'{ ... capacity policy partial-JSON representation ... }'
```

**Примечание**. для вступления в силу изменений в политике емкости кластера может потребоваться до 1 часа.

**Примеры:**

* Явное изменение всех свойств политики кластера:

```kusto
.alter cluster policy capacity
'{'
  '"IngestionCapacity": {'
    '"ClusterMaximumConcurrentOperations": 512,'
    '"CoreUtilizationCoefficient": 0.75'
  '},'
  '"ExtentsMergeCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 1'
  '},'
  '"ExtentsPurgeRebuildCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 1'
  '},'
  '"ExportCapacity": {'
    '"ClusterMaximumConcurrentOperations": 100,'
    '"CoreUtilizationCoefficient": 0.25'
  '},'
  '"ExtentsPartitionCapacity": {'
    '"ClusterMaximumConcurrentOperations": 4'
  '}'
'}'
```

* Изменение одного свойства политики уровня кластера, оставляя все остальные свойства без изменений:

```kusto
.alter-merge cluster policy capacity
'{'
  '"ExtentsPartitionCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 4'
  '}'
'}'
```
