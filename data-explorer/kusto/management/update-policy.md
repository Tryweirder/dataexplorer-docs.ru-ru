---
title: Управление политиками обновления Kusto в Azure обозреватель данных
description: В этой статье описывается политика обновления в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/04/2020
ms.openlocfilehash: 111110ac69e726c8367af4a2741a79061df7531a
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803868"
---
# <a name="update-policy-commands"></a>команды политики обновления

[Политика обновления](updatepolicy.md) — это объект политики уровня таблицы, который автоматически выполняет запрос и принимает результаты при получении данных в другую таблицу.

## <a name="show-update-policy"></a>Отображение политики обновления

Эта команда возвращает политику обновления указанной таблицы или всех таблиц в базе данных по умолчанию, если `*` используется в качестве имени таблицы.

### <a name="syntax"></a>Синтаксис

* `.show``table` *TableName* TableName `policy``update`
* `.show``table` *Имя_базы_данных*, `.` *TableName* TableName `policy``update`
* `.show` `table` `*` `policy` `update`

### <a name="returns"></a>Возвращаемое значение

Эта команда возвращает таблицу с одной записью на таблицу.

|Столбец    |Type    |Описание                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|Имя сущности, на которой определена политика обновления                                                                                                                |
|Политики  |`string`|Массив JSON, указывающий все политики обновления, определенные для сущности, в формате [объекта политики обновления](updatepolicy.md#the-update-policy-object)|

### <a name="example"></a>Пример

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |Политики                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[TestDB]. [Дериведтаблекс]|[{"Onenableed": true, "Source": "Митаблекс", "Query": "Мюпдатефунктион ()", "IsTrue": false, "Пропагатеинжестионпропертиес": false}]|

## <a name="alter-update-policy"></a>Изменение политики обновления

Эта команда задает политику обновления указанной таблицы.

### <a name="syntax"></a>Синтаксис

* `.alter``table` *TableName* `policy` TableName `update` *Аррайофупдатеполициобжектс*
* `.alter``table` *DatabaseName* `.` *TableName* `policy` `update` *аррайофупдатеполициобжектс* TableName;

*Аррайофупдатеполициобжектс* — это массив JSON, в котором определено несколько объектов политики обновления (ноль или более).

> [!NOTE]
> * Используйте хранимую функцию для `Query` свойства объекта политики обновления.
   Вам потребуется изменить только определение функции, а не весь объект политики.
> * Если `IsEnabled` параметр имеет значение `true` , то в соответствии с установленной политикой обновления выполняются следующие проверки.
>    * `Source`— Проверяет, существует ли таблица в целевой базе данных.
>    * `Query` 
>        * Проверяет, соответствует ли схема, определенная схемой, одной из целевых таблиц
>        * Проверяет, что запрос ссылается на `source` таблицу политики обновления. 
        Определение запроса политики обновления, который не ссылается на источник, можно задать `AllowUnreferencedSourceTable=true` в свойствах *со* свойствами (см. пример ниже), но не рекомендуется из-за проблем с производительностью. Для каждого приема в исходной таблице все записи в другой таблице учитываются при выполнении политики обновления.
 >       * Проверяет, что политика не приводит к созданию цикла в цепочке политик обновления в целевой базе данных.
 > * Если параметр `IsTransactional` имеет значение `true` , то проверяет, что `TableAdmin` разрешения также проверяются на соответствие `Source` (исходная таблица).
 > * Протестируйте политику или функцию обновления для повышения производительности, прежде чем применять ее для каждого приема в исходной таблице. Дополнительные сведения см. в разделе [тестирование влияния политики обновления на производительность](updatepolicy.md#performance-impact).

### <a name="returns"></a>Возвращаемое значение

Команда задает объект политики обновления таблицы, переопределяя любую текущую политику, а затем возвращает выходные данные соответствующей команды [политики обновления таблицы](#show-update-policy) .

### <a name="example"></a>Пример

```kusto
// Create a function that will be used for update
.create function 
MyUpdateFunction()
{
    MyTableX
    | where ColumnA == 'some-string'
    | summarize MyCount=count() by ColumnB, Key=ColumnC
    | join (OtherTable | project OtherColumnZ, Key=OtherColumnC) on Key
    | project ColumnB, ColumnZ=OtherColumnZ, Key, MyCount
}

// Create the target table (if it doesn't already exist)
.set-or-append DerivedTableX <| MyUpdateFunction() | limit 0

// Use update policy on table DerivedTableX
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyUpdateFunction()", "IsTransactional": false, "PropagateIngestionProperties": false}]'
```

* Когда происходит прием в исходную таблицу, в этом случае в этой `MyTableX` таблице создаются один или несколько экстентов (сегментов данных).
* `Query`Объект, определенный в объекте политики обновления, в данном случае `MyUpdateFunction()` будет работать только в этих экстентах и не будет выполняться для всей таблицы.
  * "Определение области" выполняется внутренне и автоматически, и его не следует обрабатывать при определении `Query` .
  * При приеме в производную таблицу учитываются только вновь полученные записи, которые различаются в каждой операции приема `DerivedTableX` .

```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-tablename-policy-update"></a>. ALTER-Merge таблица *имя_таблицы* обновление политики

Эта команда изменяет политику обновления указанной таблицы.

**Синтаксис**

* `.alter-merge``table` *TableName* `policy` TableName `update` *Аррайофупдатеполициобжектс*
* `.alter-merge``table` *DatabaseName* `.` *TableName* `policy` `update` *аррайофупдатеполициобжектс* TableName;

*Аррайофупдатеполициобжектс* — это массив JSON, в котором определено несколько объектов политики обновления (ноль или более).

> [!NOTE]
> * Используйте хранимые функции для выполнения групповой реализации свойства Query объекта политики обновления. 
     Необходимо изменить только определение функции, а не весь объект политики.
> * Проверки совпадают с действиями, выполненными в `alter` команде.

**Возвращает**

Команда добавляет к объекту политики обновления таблицы, переопределяя любую текущую политику, а затем возвращает выходные данные соответствующей команды [. отобразить таблицу *TableName* Update Policy](#show-update-policy) .

**Пример**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-tablename-policy-update"></a>. Удаление таблицы *TableName* с обновлением политики

Удаляет политику обновления указанной таблицы.

**Синтаксис**

* `.delete``table` *TableName* TableName `policy``update`
* `.delete``table` *Имя_базы_данных*, `.` *TableName* TableName `policy``update`

**Возвращает**

Команда удаляет объект политики обновления таблицы, а затем возвращает выходные данные соответствующей команды [. отобразить таблицу *TableName* Update Policy](#show-update-policy) .

**Пример**

```kusto
.delete table DerivedTableX policy update 
```
