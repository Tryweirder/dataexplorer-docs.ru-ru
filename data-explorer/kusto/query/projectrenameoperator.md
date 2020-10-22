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
ms.openlocfilehash: 9c46c8d0271516c56c3549212ad7b74f721ce81b
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356509"
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
