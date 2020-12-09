---
title: Оператор Project в Azure Data Explorer | Документация Майкрософт
description: В этой статье описан оператор Project в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: f94003573cab076d8fa83537cb7868e21b9b084c
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513273"
---
# <a name="project-operator"></a>Оператор project

Выберите столбцы для включения, переименования или удаления, а затем вставьте новые вычисляемые столбцы. 

Порядок столбцов в результате определяется порядком аргументов. В результат будут включены только указанные в аргументах столбцы. Другие столбцы во входных данных удаляются.  (См. также сведения об `extend`.)

```kusto
T | project cost=price*quantity, price
```

## <a name="syntax"></a>Синтаксис

*T* `| project` *ColumnName* [`=` *Expression*] [`,` ...]
  
или
  
*T* `| project` [*ColumnName* | `(`*ColumnName*[`,`]`)` `=`] *Expression* [`,` ...]

## <a name="arguments"></a>Аргументы

* *T*: входная таблица.
* *ColumnName*: необязательное имя столбца, который будет отображен в выходных данных. Если аргумент *Expression* отсутствует, параметр *ColumnName* нужно указать обязательно и столбец с таким именем должен присутствовать во входных данных. Если имя опущено, оно будет создано автоматически. Если *Expression* возвращает более одного столбца, список имен столбцов можно указать в скобках. В этом случае выходные столбцы *Expression* получат указанные имена, а все остальные выходные столбцы (при наличии) будут удалены. Если список имен столбцов не указан, все выходные столбцы *Expression* с созданными именами будут добавлены в выходные данные.
* *Expression* — необязательное скалярное выражение, указывающее на входные столбцы. Если *ColumnName* не опускается, выражение *Expression* является обязательным.

    Можно получить новый вычисляемый столбец с именем существующего столбца во входных данных.

## <a name="returns"></a>Возвращаемое значение

Таблица со столбцами, именованными как аргументы, а также количеством строк, соответствующим входной таблице.

## <a name="example"></a>Пример

В следующем примере показано несколько типов операций, которые можно выполнить с помощью оператора `project` . Входная таблица `T` содержит три столбца типа `int`: `A`, `B` и `C`. 

```kusto
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```

[series_stats](series-statsfunction.md) — пример функции, возвращающей несколько столбцов.