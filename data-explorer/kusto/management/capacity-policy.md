---
title: Команды управления емкости - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны команды управления системой емкости в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/02/2020
ms.openlocfilehash: 929cfa885a7373b400b832d908677a7a5fb93ef6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522089"
---
# <a name="capacity-policy-control-commands"></a>Команды управления политикой емкости

Для управления [политикой емкости](../management/capacitypolicy.md)кластера можно использовать следующие команды управления.

Команды требуют разрешений [AllDatabasesAdmin.](../management/access-control/role-based-authorization.md)

## <a name="show-cluster-policy-capacity"></a>показать способность кластерной политики

```kusto
.show cluster policy capacity
```

Отображает текущую политику емкости для кластера.

**Вывод**

|Имя политики | Имя сущности | Политика | Дочерние сущности | Тип сущности
|---|---|---|---|---
|ЕмкостьПолитика | | Строка формата JSON, представляющая политику | Список баз данных в кластере |Кластер


## <a name="alter-cluster-policy-capacity"></a>изменить потенциал кластерной политики

```kusto
.alter cluster policy capacity @'{ ... capacity policy JSON representation ... }'
.alter-merge cluster policy capacity @'{ ... capacity policy partial-JSON representation ... }'
```

**Примечание**: Изменения в политике емкости кластера могут занять до 1 часа, чтобы ввести в действие.

**Примеры:**

* Явноизменяя все свойства кластерной политики:

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

* Изменение одного свойства политики уровня кластера, сохранение всех других свойств нетронутыми:

```kusto
.alter-merge cluster policy capacity
'{'
  '"ExtentsPartitionCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 4'
  '}'
'}'
```
