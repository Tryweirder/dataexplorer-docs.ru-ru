---
title: оператор по потреблению - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается оператор потребления в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 65c2f2befc074042131b5c0d705fa942a1622035
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517125"
---
# <a name="consume-operator"></a>Оператор consume

Потребляет табликный поток данных, передаваемый оператору. 

Оператор `consume` в основном используется для запуска побочного эффекта запроса без фактического возврата результатов обратно вызывающему абоненту.

```kusto
T | consume
```

**Синтаксис**

`consume``decodeblocks` `=` *Декодированиеблоков*

**Аргументы**

* *Декодирование*: Постоянное значение Boolean. Если `true`установлено, или если `perftrace` свойство `true`запроса установлено, `consume` оператор будет не только перечислить записи на его ввод, но на самом деле заставить каждое значение в этих записях, чтобы быть разжатым и декодированы.

Оператор `consume` может быть использован для оценки стоимости запроса без фактической доставки результатов обратно клиенту.
(Оценка не является точной по целому `consume` ряду причин; например, рассчитывается распределительно, поэтому `T | consume` не будет передавать данные таблицы между узлами кластера.)

<!--
* *WithStats*: A constant Boolean value. If set to `true` (or if the global
  property `perftrace` is set), the operator will return a single
  row with a single column called `Stats` of type `dynamic` holding the statistics
  of the data source fed to the `consume` operator.
-->