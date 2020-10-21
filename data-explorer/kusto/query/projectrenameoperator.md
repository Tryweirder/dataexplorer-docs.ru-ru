---
title: проект-переименование оператора Azure обозреватель данных
description: В этой статье описывается оператор переименования проекта в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 83a4dba3f426189de615640ab48e1fa60f9e5537
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242208"
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

|new_a|new_b|c|
|---|---|---|
|а|b|c|
