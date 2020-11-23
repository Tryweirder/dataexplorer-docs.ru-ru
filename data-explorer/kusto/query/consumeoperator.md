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
ms.openlocfilehash: 4b2a9d100cdb7125ebed21be69433fa3cee3a929
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324539"
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
