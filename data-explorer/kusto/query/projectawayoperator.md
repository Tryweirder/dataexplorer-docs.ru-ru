---
title: Оператор проекта-выезда - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается оператор проекта в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 38ec57e9659458ef34117e4a380c756310db218b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510954"
---
# <a name="project-away-operator"></a>Оператор project-away

Выберите, какие столбцы в вводах исключить из вывода

```kusto
T | project-away price, quantity, zz*
```

Порядок столбцов в результате определяется их исходным порядком в таблице. Отбрасывается только столбцы, указанные в качестве аргументов. Другие столбцы включены в результат.  (См. также сведения об `project`.)

**Синтаксис**

*T* `| project-away` *КолонкаНаименОрШаблон* `,`

**Аргументы**

* *T*: Таблица ввода
* *КолонкаНамейзорШаблон:* Имя столбца или шаблона подстановочных знаков, которые должны быть удалены из вывода.

**Возвращает**

Таблица с столбцов, которые не были названы в качестве аргументов. Содержит такое же количество строк, что и таблица ввода.

**Советы**

* Используйте, [`project-rename`](projectrenameoperator.md) если вы намереваетесь переименовать столбцы.
* Используйте, [`project-reorder`](projectreorderoperator.md) если вы намереваетесь переупорядочить столбцы.

* Можно `project-away` можно вычислить любые столбцы, присутствующие в исходной таблице или вычисляемые как часть запроса.


**Примеры**

Входная таблица `T` содержит три столбца типа `long`: `A`, `B` и `C`.

```kusto
datatable(A:long, B:long, C:long) [1, 2, 3]
| project-away C    // Removes column C from the output
```

|Объект|B|
|---|---|
|1|2|

Удаление столбцов, начиная с 'a'.

```kusto
print  a2='a2', b = 'b', a3='a3', a1='a1'
|  project-away a* 
```

|b|
|---|
|b|

