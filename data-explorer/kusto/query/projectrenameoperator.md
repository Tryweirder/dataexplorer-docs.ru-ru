---
title: проект-переименование оператора Azure обозреватель данных
description: В этой статье описывается оператор переименования проекта в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 68581cfe4b3828823ced7d4704eb08df5d2aefa7
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373178"
---
# <a name="project-rename-operator"></a>Оператор project-rename

Переименовывает столбцы в выходных данных результатов.

```kusto
T | project-rename new_column_name = column_name
```

**Синтаксис**

*T* `| project-rename` *невколумннаме*  =  *ексистингколумннаме* [ `,` ...]

**Аргументы**

* *T*: входная таблица.
* *Невколумннаме:* Новое имя столбца. 
* *Ексистингколумннаме:* Существующее имя столбца. 

**Возвращает**

Таблица, в которой столбцы находятся в том же порядке, что и в существующей таблице, с переименованными столбцами.


**Примеры**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|с|
|---|---|---|
|а|b|с|
