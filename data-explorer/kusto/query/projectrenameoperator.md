---
title: Оператор переименования проекта - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается оператор переименования проекта в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1f7fbf2efbfd3ed1dfac9129ec21f5a13c51481c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510869"
---
# <a name="project-rename-operator"></a>Оператор project-rename

Переименование столбцов в выходе результата.

```kusto
T | project-rename new_column_name = column_name
```

**Синтаксис**

*T* `| project-rename` *NewColumnName* = *СуществующиеКолобейкИмен* `,`

**Аргументы**

* *T*: Таблица ввода.
* *NewColumnNameName:* Новое название столбца. 
* *СуществующийКолпогенам:* Существующее название столбца. 

**Возвращает**

Таблица с столбцов в том же порядке, что и в существующей таблице, с переименованными столбцов.


**Примеры**

```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|с|
|---|---|---|
|а|b|с|