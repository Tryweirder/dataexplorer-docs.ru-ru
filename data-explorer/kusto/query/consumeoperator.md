---
title: Использование оператора обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается использование оператора в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 1e4b2ff4ee45fd92e7e16f9468c18e1d0a70ca82
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245172"
---
# <a name="consume-operator"></a>Оператор consume

Использует поток табличных данных, переданный оператору. 

`consume`Оператор в основном используется для срабатывания побочного действия запроса, не возвращая результаты обратно вызывающему объекту.

```kusto
T | consume
```

## <a name="syntax"></a>Синтаксис

`consume` [ `decodeblocks` `=` *Декодеблоккс*]

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