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
ms.openlocfilehash: 2938019237c882891af8ff86f4d33de3605a9063
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356526"
---
# <a name="project-away-operator"></a>Оператор project-away

Выберите столбцы из входных данных, которые следует исключить из выходных данных.

```kusto
T | project-away price, quantity, zz*
```

Порядок столбцов в результатах определяется их исходным порядком в таблице. Удаляются только те столбцы, которые были указаны в качестве аргументов. В результат включаются другие столбцы. (См. также сведения об `project`.)

## <a name="syntax"></a>Синтаксис

*T* `| project-away` *колумннамеорпаттерн* [ `,` ...]

## <a name="arguments"></a>Аргументы

* *T*: входная таблица
* *Колумннамеорпаттерн:* Имя шаблона столбца или столбца, который должен быть удален из выходных данных.

## <a name="returns"></a>Результаты

Таблица со столбцами, которые не были именованы в качестве аргументов. Содержит то же количество строк, что и входная таблица.

> [!TIP]
>
> * Для переименования столбцов используйте [`project-rename`](projectrenameoperator.md) .
> * Чтобы изменить порядок столбцов, используйте [`project-reorder`](projectreorderoperator.md) .
> * Можно также иметь `project-away` любые столбцы, которые имеются в исходной таблице или которые были вычислены как часть запроса.

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
print a2='a2', b = 'b', a3='a3', a1='a1'
| project-away a*
```

|b|
|---|
|b|

## <a name="see-also"></a>См. также статью

Чтобы выбрать столбцы из входных данных для сохранения в выходных данных, используйте [Project-держитесь](project-keep-operator.md).
