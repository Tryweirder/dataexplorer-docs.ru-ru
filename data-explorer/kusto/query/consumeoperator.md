---
title: Использование оператора обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается использование оператора в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 85fd891590e359e31224ed5d707a837b1cc0eb41
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348842"
---
# <a name="consume-operator"></a>Оператор consume

Использует поток табличных данных, переданный оператору. 

`consume`Оператор в основном используется для срабатывания побочного действия запроса, не возвращая результаты обратно вызывающему объекту.

```kusto
T | consume
```

## <a name="syntax"></a>Синтаксис

`consume`[ `decodeblocks` `=` *Декодеблоккс*]

## <a name="arguments"></a>Аргументы

* *Декодеблоккс*: константное логическое значение. Если задано значение `true` или для свойства запроса `perftrace` задано значение `true` , `consume` оператор не будет перечислять записи во входных данных, но фактически принудительно вынуждает каждое значение в этих записях быть распаковано и декодировано.

`consume`Оператор можно использовать для оценки стоимости запроса без фактической передачи результатов клиенту.
(Оценка не является точной по ряду причин; например, `consume` вычисляется отдельно, поэтому `T | consume` не передает данные таблицы между узлами кластера.)

<!--
* *WithStats*: A constant Boolean value. If set to `true` (or if the global
  property `perftrace` is set), the operator will return a single
  row with a single column called `Stats` of type `dynamic` holding the statistics
  of the data source fed to the `consume` operator.
-->