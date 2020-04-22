---
title: Кросс-база данных и кросс-кластерные запросы - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны запросы о перекрестной базе данных и кросс-кластерах в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8bb0cf2674bae801fb98d14d93518f5617af6807
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766068"
---
# <a name="cross-database-and-cross-cluster-queries"></a>Запросы между базами данных и между кластерами

::: zone pivot="azuredataexplorer"

Каждый запрос Kusto работает в контексте текущего кластера и базы данных по умолчанию.
* В [Kusto Explorer](../tools/kusto-explorer.md) база данных по умолчанию выбрана в [панели соединений,](../tools/kusto-explorer.md#connections-panel) а текущий кластер — это соединение, содержащее эту базу данных
* При использовании [библиотеки клиентов Kusto](../api/netfx/about-kusto-data.md) текущий `Data Source` кластер `Initial Catalog` и база данных по умолчанию определяются [строками соединения Kusto](../api/connection-strings/kusto.md) соответственно.

## <a name="queries"></a>Запросы
Для доступа к таблицам из любой базы данных, кроме по умолчанию, необходимо использовать квалифицированный синтаксис *имени:* Для доступа к базе данных в текущем кластере:
```kusto
database("<database name>").<table name>
```
База данных в удаленном кластере:
```kusto
cluster("<cluster name>").database("<database name>").<table name>
```

*имя базы данных* является чувствительным к случаям

*название кластера* является нечувствительным и может быть одной из следующих форм:
* Хорошо сформированный `http://contoso.kusto.windows.net:1234/`URL: например, поддерживаются только схемы HTTP и HTTPS.
* Полностью квалифицированное доменное имя `contoso.kusto.windows.net` (ФЗДН): например, которое будет эквивалентно`https://`**`contoso.kusto.windows.net`**`:443/`
* Короткое имя (имя хозяина (и область) `contoso` без доменной `https://` **`contoso`** `.kusto.windows.net:443/`части): `contoso.westus` например, что интерпретируется как или интерпретируется как`https://`**`contoso.westus`**`.kusto.windows.net:443/`

> [!NOTE]
> Кросс-база данных подвергается обычной проверке разрешений.
> Чтобы выслать запрос, необходимо прочитать разрешение в базе данных по умолчанию и во все другие базы данных, на которые ссылаются в запросе (в текущих и удаленных кластерах).

*Квалифицированное имя* может быть использовано в любом контексте, в котором можно использовать имя таблицы.
Все следующие действия действительны:

```kusto
database("OtherDb").Table | where ...

union Table1, cluster("OtherCluster").database("OtherDb").Table2 | project ...

database("OtherDb1").Table1 | join cluster("OtherCluster").database("OtherDb2").Table2 on Key | join Table3 on Key | extend ...
```

Когда *квалифицированное имя* отображается в качестве работы [оператора профсоюза,](./unionoperator.md)подстановочные знаки могут быть использованы для указания нескольких таблиц и нескольких баз данных. Wildcards не допускаются в названиях кластеров:

```kusto
union withsource=TableName *, database("OtherDb*").*Table, cluster("OtherCluster").database("*").*
```

> [!NOTE]
>* Имя базы данных по умолчанию также потенциально совпадает, поэтому база данных ("&#42;") определяет все таблицы всех баз данных, включая значение по умолчанию.
>* Как изменения схем влияют на запросы кросс-кластера, см. [запросы кросс-кластера и изменения схемы](../concepts/crossclusterandschemachanges.md)

## <a name="access-restriction"></a>Ограничение доступа 
Квалифицированные имена или шаблоны также могут быть включены в заявление [об ограничении доступа](./restrictstatement.md) (не допускаются карты в группах)
```kusto
restrict access to (my*, database("MyOther*").*, cluster("OtherCluster").database("my2*").*);
```

Вышеупомянутое ограничит доступ к запросу следующим:

* Любое имя сущности, начиная с *моего...* в базе данных по умолчанию. 
* Любая таблица во всех базах данных называется *MyOther...*
* Любая таблица во всех базах данных называется *my2...* в кластере *OtherCluster.kusto.windows.net*.

## <a name="functions-and-views"></a>Функции и представления

Функции и представления (постоянные и созданные в строке) могут ссылаться на таблицы баз данных и границ кластера. Следующие действия:

```kusto
let MyView = Table1 join database("OtherDb").Table2 on Key | join cluster("OtherCluster").database("SomeDb").Table3 on Key;
MyView | where ...
```

Постоянные функции и представления могут быть доступны из другой базы данных в том же кластере:

Табулярная функция `OtherDb`(вид) в:

```kusto
.create function MyView(v:string) { Table1 | where Column1 has v ...  }  
```

Функция scalar `OtherDb`в:
```kusto
.create function MyCalc(a:double, b:double, c:double) { (a + b) / c }  
```

В базе данных по умолчанию:

```kusto
database("OtherDb").MyView("exception") | extend CalCol=database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

## <a name="limitations-of-cross-cluster-function-calls"></a>Ограничения вызовов функций кросс-кластера

Табулярные функции или представления могут быть отсылками в разных кластерах. Применяется следующее ограничение:

1. Удаленная функция должна вернуть табелярную схему. Функции Scalar доступны только в том же кластере.
2. Удаленная функция может принимать только параметры масштабирования. Функции, которые получают один или несколько аргументов таблицы, могут быть доступны только в том же кластере.
3. Схема удаленной функции должна быть известна и инвариантна ее параметров (см. также [кросс-кластерные запросы и изменения схемы).](../concepts/crossclusterandschemachanges.md)

Следующий кросс-кластерный вызов **действителен:**

```kusto
cluster("OtherCluster").database("SomeDb").MyView("exception") | count
```

Следующий запрос вызывает функцию `MyCalc`удаленного масштабирования.
Это нарушает правило #1, поэтому оно **не действительно:**

```kusto
MyTable | extend CalCol=cluster("OtherCluster").database("OtherDb").MyCalc(Col1, Col2, Col3) | limit 10
```

Следующий запрос вызывает `MyCalc` удаленную функцию и обеспечивает таблообразный параметр.
Это нарушает правило #2, поэтому оно **не действительно:**

```kusto
cluster("OtherCluster").database("OtherDb").MyCalc(datatable(x:string, y:string)["x","y"] ) 
```

Следующий запрос вызывает `SomeTable` удаленную функцию, которая имеет `tablename`выход переменной схемы на основе параметра.
Это нарушение правила #3, поэтому оно **не является действительным:**

Табулярная `OtherDb`функция в:
```kusto
.create function SomeTable(tablename:string) { table(tablename)  }  
```

В базе данных по умолчанию:
```kusto
cluster("OtherCluster").database("OtherDb").SomeTable("MyTable")
```

Следующий запрос вызывает `GetDataPivot` удаленную функцию, которая имеет выход переменной схемы на основе данных[(плагин ключа()](pivotplugin.md) имеет динамический выход).
Это нарушение правила #3, поэтому оно **не является действительным:**

Табулярная `OtherDb`функция в:
```kusto
.create function GetDataPivot() { T | evaluate pivot(PivotColumn) }  
```

Табулярная функция в базе данных по умолчанию:
```kusto
cluster("OtherCluster").database("OtherDb").GetDataPivot()
```

## <a name="displaying-data"></a>Отображение данных

Заявления, возвращающие данные клиенту, неявно ограничены количеством возвращенных записей, `take` даже если оператор не использует конкретное. Чтобы снять это ограничение, используйте параметр `notruncation` клиентского запроса.

Для отображения данных в графической форме используйте [оператор визуализации.](renderoperator.md)

::: zone-end

::: zone pivot="azuremonitor"

Это не поддерживается в Azure Monitor

::: zone-end
