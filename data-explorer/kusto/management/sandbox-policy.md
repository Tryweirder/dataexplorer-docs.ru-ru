---
title: Политика песочницы - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается политика песочницы в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 7ac5a92b2084eaf2b447f296be34b2f4e79e1bb7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520134"
---
# <a name="sandbox-policy"></a>Политика песочницы

Следующие команды позволяют управление [песочницами](../concepts/sandboxes.md) и [политиками песочницы](sandboxpolicy.md) в службе двигателя Kusto.

Команды требуют разрешений [AllDatabasesAdmin.](access-control/role-based-authorization.md)

## <a name="sandbox-policy"></a>Политика песочницы

### <a name="show-cluster-policy-sandbox"></a>.show кластерной политики песочнице

Отображается все настроенные политики песочницы на уровне кластера.

```kusto
.show cluster policy sandbox
```

### <a name="alter-cluster-policy-sandbox"></a>.alter кластерной политики песочнице

Изменяет сбор политик песочницы на уровне кластера.

```kusto
.alter cluster policy sandbox @'['
  '{'
    '"SandboxKind": "PythonExecution",'
    '"IsEnabled": true,'
    '"TargetCountPerNode": 4,'
    '"MaxCpuRatePerSandbox": 50,'
    '"MaxMemoryMbPerSandbox": 10240'
  '},'
  '{'
    '"SandboxKind": "RExecution",'
    '"IsEnabled": true,'
    '"TargetCountPerNode": 4,'
    '"MaxCpuRatePerSandbox": 50,'
    '"MaxMemoryMbPerSandbox": 10240'
  '}'
']'
```

### <a name="drop-cluster-policy-sandbox"></a>.drop кластерной политики песочнице

Для отказа от **всех** политик песочницы используйте следующую команду:

```kusto
.delete cluster policy sandbox
```

