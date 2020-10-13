---
title: Политика "песочницы" — Azure обозреватель данных | Документация Майкрософт
description: Сведения о командах политики песочницы в Azure обозреватель данных. См. статью как показать, настроить и удалить политики песочницы.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: ba37147db87c436aff7d77790641b17576e86392
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002972"
---
# <a name="sandbox-policy-command"></a>Команда политики "песочницы"

Следующие команды позволяют управлять [песочницами](../concepts/sandboxes.md) и [политиками песочницы](sandboxpolicy.md) в службе Kusto Engine.

Для команд требуются разрешения [аллдатабасесадмин](access-control/role-based-authorization.md) .

## <a name="sandbox-policy"></a>Политика песочницы

### <a name="show-cluster-policy-sandbox"></a>. Отображение песочницы политики кластера

Отображает все настроенные политики песочницы на уровне кластера.

```kusto
.show cluster policy sandbox
```

### <a name="alter-cluster-policy-sandbox"></a>. ALTER "песочница политики кластера"

Изменяет коллекцию политик песочницы на уровне кластера.

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

### <a name="drop-cluster-policy-sandbox"></a>. Удаление песочницы политики кластера

Для удаления **всех** политик "песочницы" используйте следующую команду:

```kusto
.delete cluster policy sandbox
```
