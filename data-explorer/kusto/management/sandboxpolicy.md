---
title: Политика "песочницы" — Azure обозреватель данных
description: В этой статье описывается политика "песочницы" в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 786f771878a7216b62dce127391f0e7967e954f6
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512459"
---
# <a name="sandbox-policy"></a>Политика песочницы

Обозреватель данных Azure выполняет определенные подключаемые модули в [песочницах](../concepts/sandboxes.md) , доступные ресурсы которых ограничены и контролируются для обеспечения безопасности и управления ресурсами.

Песочницы выполняются на узлах подсистемы Kusto. Некоторые из их ограничений определяются в политиках "песочницы", где у каждого типа "песочницы" может быть собственная политика.

Политики "песочницы" управляются на уровне кластера и влияют на все узлы в кластере.

Чтобы изменить политики, необходимы разрешения [аллдатабасесадмин](../management/access-control/role-based-authorization.md) .

## <a name="the-policy-object"></a>Объект политики

Политика "песочницы" имеет следующие свойства.

* **Сандбокскинд**: определяет тип "песочницы" (например,, `PythonExecution` , `RExecution` ).
* **Включено**: определяет, могут ли песочницы этого типа запускаться на узлах кластера.
* **Таржеткаунтперноде**: определяет, сколько изолированных программ этого типа разрешено выполнять на узлах кластера.
  * Значения могут быть между двумя и вдвое числом процессоров на каждый узел.
  * Значение по умолчанию — 16.
* **Макскпуратеперсандбокс**: определяет максимальную скорость ЦП в процентах от всех доступных ядер, которые может использовать одна песочница.
  * Может принимать значения от 1 до 100.
  * Значение по умолчанию: 50.
* **Максмеморимбперсандбокс**: определяет максимальный объем памяти (в мегабайтах), который может использоваться одной песочницей.
  * Может принимать значения от 200 до 65536 (64 ГБ).
  * Значение по умолчанию — 20480 (20 ГБ).

## <a name="example"></a>Пример

Следующая политика задает различные ограничения для `PythonExecution` и `RExecution` песочниц:

```json
[
  {
    "SandboxKind": "PythonExecution",
    "IsEnabled": true,
    "TargetCountPerNode": 4,
    "MaxCpuRatePerSandbox": 55,
    "MaxMemoryMbPerSandbox": 65536
  },
  {
    "SandboxKind": "RExecution",
    "IsEnabled": true,
    "TargetCountPerNode": 2,
    "MaxCpuRatePerSandbox": 50,
    "MaxMemoryMbPerSandbox": 10240
  }
]
```

> [!NOTE]
> * Изменения политики "песочницы" применяются к изолированным средам, созданным начиная с момента применения изменения. Песочницы, предварительно выделенные перед изменением политики, будут продолжать выполняться в соответствии с предыдущими ограничениями политики, пока они не будут использоваться в качестве части запроса.
> * Возможна задержка до пяти минут, пока изменение политики не вступит в силу, так как узлы кластера периодически опрашиваются на предмет изменений политики.

## <a name="next-steps"></a>Следующие шаги

Используйте [команды управления политиками "песочницы"](../management/sandbox-policy.md) для управления политикой "песочницы" кластера.
 
