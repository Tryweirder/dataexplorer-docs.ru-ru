---
title: Политика "песочницы" — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается политика "песочницы" в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 7d56d602f53db29f5ea558acb0e9e4288e5ac6e3
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967491"
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

