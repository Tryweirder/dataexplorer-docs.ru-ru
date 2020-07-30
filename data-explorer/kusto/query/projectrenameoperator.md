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
ms.openlocfilehash: 9bc000ffa57d906c3e65e54e9daac5431f8dc276
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346020"
---
# <a name="project-rename-operator"></a>Оператор project-rename

Переименовывает столбцы в выходных данных результатов.

```kusto
T | project-rename new_column_name = column_name
```

## <a name="syntax"></a>Синтаксис

*T* `| project-rename` *невколумннаме*  =  *ексистингколумннаме* [ `,` ...]

## <a name="arguments"></a>Аргументы

* *T*: входная таблица.
* *Невколумннаме:* Новое имя столбца. 
* *Ексистингколумннаме:* Существующее имя столбца. 

## <a name="returns"></a>Результаты

Таблица, в которой столбцы находятся в том же порядке, что и в существующей таблице, с переименованными столбцами.


## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|с|
|---|---|---|
|a|b|с|
