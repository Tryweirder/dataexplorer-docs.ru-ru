---
title: Оператор отсутствия на стороне проекта — Azure обозреватель данных
description: В этой статье описывается оператор проекции в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a708a83e4bef1c1d9b774f0304e2dd8c7cba8cda
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244785"
---
# <a name="project-away-operator"></a>Оператор project-away

Выберите столбцы во входных данных, которые следует исключить из выходных данных

```kusto
T | project-away price, quantity, zz*
```

Порядок столбцов в результатах определяется их исходным порядком в таблице. Удаляются только те столбцы, которые были указаны в качестве аргументов. В результат включаются другие столбцы.  (См. также сведения об `project`.)

## <a name="syntax"></a>Синтаксис

*T* `| project-away` *колумннамеорпаттерн* [ `,` ...]

## <a name="arguments"></a>Аргументы

* *T*: входная таблица
* *Колумннамеорпаттерн:* Имя шаблона столбца или столбца, который должен быть удален из выходных данных.

## <a name="returns"></a>Результаты

Таблица со столбцами, которые не были именованы в качестве аргументов. Содержит то же количество строк, что и входная таблица.

**Советы**

* Используйте [`project-rename`](projectrenameoperator.md) , если ваша цель заключается в переименовании столбцов.
* Используйте [`project-reorder`](projectreorderoperator.md) , если ваша цель заключается в переупорядочении столбцов.

* Можно также иметь `project-away` любые столбцы, которые имеются в исходной таблице или которые были вычислены как часть запроса.


## <a name="examples"></a>Примеры

Входная таблица `T` содержит три столбца типа `long`: `A`, `B` и `C`.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(A:long, B:long, C:long) [1, 2, 3]
| project-away C    // Removes column C from the output
```

|Объект|B|
|---|---|
|1|2|

Удаление столбцов, начинающихся с "a".

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print  a2='a2', b = 'b', a3='a3', a1='a1'
|  project-away a* 
```

|b|
|---|
|b|

