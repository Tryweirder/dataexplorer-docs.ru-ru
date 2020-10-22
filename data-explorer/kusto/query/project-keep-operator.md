---
title: Оператор сохранения проекта в Azure обозреватель данных
description: В этой статье описывается оператор сохранения проекта в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/21/2020
ms.openlocfilehash: 2efc06ad701cc734ffad0bec7d89e3d3ef1d390d
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356728"
---
# <a name="project-keep-operator"></a>Оператор сохранения проекта

Выберите столбцы из входных данных для сохранения в выходных данных.

```kusto
T | project-keep price, quantity, zz*
```

Порядок столбцов в результатах определяется их исходным порядком в таблице. Сохраняются только столбцы, указанные в качестве аргументов. Другие столбцы исключаются из результата. См. также [`project`](projectoperator.md).

## <a name="syntax"></a>Синтаксис

*T* `| project-keep` *колумннамеорпаттерн* [ `,` ...]

## <a name="arguments"></a>Аргументы

* *T*: входная таблица
* *Колумннамеорпаттерн:* Имя шаблона столбца или столбца, который должен храниться в выходных данных.

## <a name="returns"></a>Результаты

Таблица со столбцами, которые были именованы в качестве аргументов. Содержит то же количество строк, что и входная таблица.

> [!TIP]
>* Для переименования столбцов используйте [`project-rename`](projectrenameoperator.md) .
>* Чтобы изменить порядок столбцов, используйте [`project-reorder`](projectreorderoperator.md) .
>* Можно также иметь `project-keep` любые столбцы, которые имеются в исходной таблице или которые были вычислены как часть запроса.

## <a name="example"></a>Пример

Входная таблица `T` содержит три столбца типа `long`: `A`, `B` и `C`.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(A1:long, A2:long, B:long) [1, 2, 3]
| project-keep A*    // Keeps only columns A1 and A2 in the output
```

|A1|A2|
|---|---|
|1|2|

## <a name="see-also"></a>См. также статью

Чтобы выбрать столбцы из входных данных, исключаемые из выходных данных, используйте [проекцию](projectawayoperator.md).
