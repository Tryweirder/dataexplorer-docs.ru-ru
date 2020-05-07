---
title: Запросы между базами данных и перекрестными кластерами в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются запросы между базами данных и перекрестными кластерами в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 834fd81e1832b8ab624da8d99cb5cc32407db84f
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741756"
---
# <a name="cross-database-and-cross-cluster-queries"></a>Запросы между базами данных и между кластерами

::: zone pivot="azuredataexplorer"

Каждый запрос Kusto работает в контексте текущего кластера и базы данных по умолчанию.
* В [обозревателе Kusto](../tools/kusto-explorer.md) база данных по умолчанию выбрана на [панели подключения](../tools/kusto-explorer.md#connections-panel) , а текущий кластер — это подключение, содержащее эту базу данных.
* При использовании [клиентской библиотеки Kusto](../api/netfx/about-kusto-data.md) текущий кластер и база данных по умолчанию `Data Source` задаются `Initial Catalog` свойствами и [строк подключения Kusto](../api/connection-strings/kusto.md) соответственно.

## <a name="queries"></a>Запросы
Для доступа к таблицам из любой базы данных, отличной от используемой по умолчанию, необходимо использовать синтаксис *полного имени* : для доступа к базе данных в текущем кластере:
```kusto
database("<database name>").<table name>
```
База данных в удаленном кластере:
```kusto
cluster("<cluster name>").database("<database name>").<table name>
```

*имя базы данных* чувствительно к регистру

*имя кластера* не учитывает регистр и может быть одной из следующих форм:
* URL-адрес правильного формата: `http://contoso.kusto.windows.net:1234/`например, поддерживаются только схемы HTTP и HTTPS.
* Полное доменное имя (FQDN): для экземпляра `contoso.kusto.windows.net` , который будет эквивалентен`https://`**`contoso.kusto.windows.net`**`:443/`
* Краткое имя (имя узла [и регион] без доменной части): для экземпляра `contoso` , который интерпретируется как `https://` **`contoso`** `.kusto.windows.net:443/`, или `contoso.westus` , который интерпретируется как`https://`**`contoso.westus`**`.kusto.windows.net:443/`

> [!NOTE]
> Доступ между базами данных зависит от обычных проверок разрешений.
> Чтобы екскуте запрос, необходимо иметь разрешение на чтение для базы данных по умолчанию и для всех остальных баз данных, на которые ссылается запрос (в текущем и удаленном кластерах).

*Полное имя* можно использовать в любом контексте, в котором можно использовать имя таблицы.
Допустимы следующие действия.

```kusto
database("OtherDb").Table | where ...

union Table1, cluster("OtherCluster").database("OtherDb").Table2 | project ...

database("OtherDb1").Table1 | join cluster("OtherCluster").database("OtherDb2").Table2 on Key | join Table3 on Key | extend ...
```

Если *полное имя* отображается как операнд [оператора UNION](./unionoperator.md), можно использовать подстановочные знаки для указания нескольких таблиц и нескольких баз данных. В именах кластеров не допускаются подстановочные знаки:

```kusto
union withsource=TableName *, database("OtherDb*").*Table, cluster("OtherCluster").database("*").*
```

> [!NOTE]
>* Имя базы данных по умолчанию также является потенциальным соответствием, поэтому база данных ("&#42;"). * указывает все таблицы всех баз данных, включая значение по умолчанию.
>* Влияние изменений схемы на запросы между кластерами см. в разделе [запросы и изменения схемы между кластерами](../concepts/crossclusterandschemachanges.md) .

## <a name="access-restriction"></a>Ограничение доступа 
Полные имена или шаблоны могут также включаться в инструкцию [ограничения доступа](./restrictstatement.md) (подстановочные знаки в именах кластеров не допускаются).
```kusto
restrict access to (my*, database("MyOther*").*, cluster("OtherCluster").database("my2*").*);
```

Приведенный выше запрос будет ограничивать доступ к следующим сущностям:

* Любое имя сущности, начинающееся с *My...* в базе данных по умолчанию. 
* Любая таблица во всех базах данных с именем *мйосер...* текущего кластера.
* Любая таблица во всех базах данных с именем *my2...* в кластере *OtherCluster.kusto.Windows.NET*.

## <a name="functions-and-views"></a>Функции и представления

Функции и представления (постоянные и созданные встроенные) могут Справочник функциям таблицы между границами базы данных и кластера. Допустимы следующие действия:

```kusto
let MyView = Table1 join database("OtherDb").Table2 on Key | join cluster("OtherCluster").database("SomeDb").Table3 on Key;
MyView | where ...
```

К постоянным функциям и представлениям можно обращаться из другой базы данных в том же кластере:

Табличная функция (представление) `OtherDb`в:

```kusto
.create function MyView(v:string) { Table1 | where Column1 has v ...  }  
```

Скалярная функция `OtherDb`в:
```kusto
.create function MyCalc(a:double, b:double, c:double) { (a + b) / c }  
```

В базе данных по умолчанию:

```kusto
database("OtherDb").MyView("exception") | extend CalCol=database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

## <a name="limitations-of-cross-cluster-function-calls"></a>Ограничения вызовов функций между кластерами

На табличные функции и представления можно ссылаться в разных кластерах. Применяются следующие ограничения.

1. Удаленная функция должна возвращать табличную схему. Доступ к скалярным функциям может осуществляться только в одном кластере.
2. Удаленная функция может принимать только скалярные параметры. Функции, получающие один или несколько табличных аргументов, доступны только в одном кластере.
3. Схема удаленной функции должна быть известна и инвариантна от ее параметров (см. также [межкластерные запросы и изменения схемы](../concepts/crossclusterandschemachanges.md)).

Следующий кросс-кластерный вызов является **допустимым**:

```kusto
cluster("OtherCluster").database("SomeDb").MyView("exception") | count
```

Следующий запрос вызывает удаленную скалярную `MyCalc`функцию.
Это нарушает правило #1, поэтому оно является **недопустимым**:

```kusto
MyTable | extend CalCol=cluster("OtherCluster").database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

Следующий запрос вызывает удаленную функцию `MyCalc` и предоставляет табличный параметр.
Это нарушает правило #2, поэтому оно является **недопустимым**:

```kusto
cluster("OtherCluster").database("OtherDb").MyCalc(datatable(x:string, y:string)["x","y"] ) 
```

Следующий запрос вызывает удаленную функцию `SomeTable` с выходными данными схемы на основе параметра `tablename`.
Это нарушает правило #3, поэтому оно является **недопустимым**:

Табличная функция `OtherDb`в:
```kusto
.create function SomeTable(tablename:string) { table(tablename)  }  
```

В базе данных по умолчанию:
```kusto
cluster("OtherCluster").database("OtherDb").SomeTable("MyTable")
```

Следующий запрос вызывает удаленную функцию `GetDataPivot` , имеющую переменную вывод схемы на основе данных (в[подключаемом модуле Pivot ()](pivotplugin.md) есть динамический выход).
Это нарушает правило #3, поэтому оно является **недопустимым**:

Табличная функция `OtherDb`в:
```kusto
.create function GetDataPivot() { T | evaluate pivot(PivotColumn) }  
```

Табличная функция в базе данных по умолчанию:
```kusto
cluster("OtherCluster").database("OtherDb").GetDataPivot()
```

## <a name="displaying-data"></a>Отображение данных

Инструкции, возвращающие данные клиенту, неявно ограничиваются числом возвращаемых записей, даже если нет специального использования `take` оператора. Чтобы снять это ограничение, используйте параметр `notruncation` клиентского запроса.

Чтобы отобразить данные в графической форме, используйте [оператор Render](renderoperator.md).

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end