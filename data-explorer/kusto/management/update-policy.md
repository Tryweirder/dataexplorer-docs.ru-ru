---
title: Управление политиками обновления Kusto в Azure обозреватель данных
description: В этой статье описывается политика обновления в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 320824b4f614ea809141167c1284282b1ef641cf
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616564"
---
# <a name="update-policy"></a>изменение политики;

[Политика обновления](updatepolicy.md) — это объект политики уровня таблицы, позволяющий автоматически выполнять запрос и принимать его результаты при передаче данных в другую таблицу.

## <a name="show-update-policy"></a>Отображение политики обновления

Эта команда возвращает политику обновления указанной таблицы или всех таблиц в базе данных по умолчанию, если `*` используется в качестве имени таблицы.

**Синтаксис**

* `.show``table` *TableName* TableName `policy``update`
* `.show``table` *DatabaseName*Имя_базы_данных`.`*TableName* , TableName `policy``update`
* `.show` `table` `*` `policy` `update`

**Возвращает**

Эта команда возвращает таблицу с одной записью на таблицу со следующими столбцами:

|Столбец    |Тип    |Описание                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|Имя сущности, на которой определена политика обновления                                                                                                                |
|Политики  |`string`|Массив JSON, указывающий все политики обновления, определенные для сущности, в формате [объекта политики обновления](updatepolicy.md#the-update-policy-object)|

**Пример**

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |Политики                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[TestDB]. [Дериведтаблекс]|[{"Onenableed": true, "Source": "Митаблекс", "Query": "Мюпдатефунктион ()", "IsTrue": false, "Пропагатеинжестионпропертиес": false}]|

## <a name="alter-update-policy"></a>Изменение политики обновления

Эта команда задает политику обновления указанной таблицы.

**Синтаксис**

* `.alter``table` *TableName* TableName `policy` *ArrayOfUpdatePolicyObjects* аррайофупдатеполициобжектс `update`
* `.alter``table` *DatabaseName*DatabaseName`.` *аррайофупдатеполициобжектс* *TableName* ; `policy` `update`

*Аррайофупдатеполициобжектс* — это массив JSON, в котором определено несколько объектов политики обновления (ноль или более).

**Примечания**

1. Рекомендуется, чтобы в одном из них использовалась хранимая функция `Query` для свойства объекта политики обновления.
   Это упрощает изменение только определения функции, а не всего объекта политики.

2. При установке политики обновления выполняются следующие проверки (в случае `IsEnabled` , если задано значение `true`):
    1. `Source`: Таблица должна существовать в целевой базе данных.
    2. `Query`: 
        * Схема, определенная схемой, должна соответствовать одной из целевых таблиц. 
        * Запрос должен ссылаться на `source` таблицу политики обновления. Определение запроса политики обновления, который *не* ссылается на источник, можно задать `AllowUnreferencedSourceTable=true` в свойствах со свойствами (см. пример ниже), но обычно не рекомендуется из-за соображений производительности (это подразумевает, что при каждом приеме в исходную таблицу *все* записи в другой таблице учитываются при выполнении политики обновления).
    3. Политика не приводит к созданию цикла в цепочке политик обновления в целевой базе данных.
    4. Если `IsTransactional` задано значение `true`, `TableAdmin` разрешения также проверяются `Source` для (исходная таблица).
  
3. Убедитесь, что вы протестируете политику или функцию обновления для повышения производительности перед их применением для каждого приема в исходной таблице. см. [здесь](updatepolicy.md#testing-an-update-policys-performance-impact).

**Возвращает**

Команда задает объект политики обновления таблицы (переопределяя любую текущую политику, если таковые имеются), а затем возвращает выходные данные соответствующей команды [. отобразить таблицу таблицы обновления политики](#show-update-policy) .

**Пример**

```kusto
// Creating function that will be used for update
.create function 
MyUpdateFunction()
{
    MyTableX
    | where ColumnA == 'some-string'
    | summarize MyCount=count() by ColumnB, Key=ColumnC
    | join (OtherTable | project OtherColumnZ, Key=OtherColumnC) on Key
    | project ColumnB, ColumnZ=OtherColumnZ, Key, MyCount
}

// Creating the target table (in case it doesn't already exist)
.set-or-append DerivedTableX <| MyUpdateFunction() | limit 0

// Use update policy on table DerivedTableX
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyUpdateFunction()", "IsTransactional": false, "PropagateIngestionProperties": false}]'
```

- Когда происходит прием в исходную таблицу (в данном случае `MyTableX`), в этой таблице создается 1 или более экстентов (сегментов данных).
- `Query` Объект, определенный в объекте политики обновления (в данном случае `MyUpdateFunction()`), будет выполняться только в этих экстентах и не будет выполняться для всей таблицы.
  - Это «определение области» выполняется внутренним образом и автоматически, его не следует обрабатывать при определении `Query`.
  - При приеме в производную таблицу `DerivedTableX`будут учитываться только вновь полученные записи (в каждой операции приема).


```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-table-policy-update"></a>. ALTER-Merge таблица таблиц обновление политики

Эта команда изменяет политику обновления указанной таблицы.

**Синтаксис**

* `.alter-merge``table` *TableName* TableName `policy` *ArrayOfUpdatePolicyObjects* аррайофупдатеполициобжектс `update`
* `.alter-merge``table` *DatabaseName*DatabaseName`.` *аррайофупдатеполициобжектс* *TableName* ; `policy` `update`

*Аррайофупдатеполициобжектс* — это массив JSON, в котором определено несколько объектов политики обновления (ноль или более).

**Примечания**

1. Рекомендуется, чтобы одна использовала хранимые функции для групповой реализации свойства Query объекта политики обновления. Это упрощает изменение только определения функции, а не всего объекта политики.

2. Те же проверки, которые выполняются в политике обновления в случае выполнения `alter` команды, выполняются для `alter-merge` команды.

**Возвращает**

Команда добавляется к объекту политики обновления таблицы (переопределяя любую текущую политику, если она есть), а затем возвращает выходные данные соответствующей команды [. отобразить](#show-update-policy) таблицу таблицы.

**Пример**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-table-policy-update"></a>. Удаление обновления политики таблицы таблиц

Удаляет политику обновления указанной таблицы.

**Синтаксис**

* `.delete``table` *TableName* TableName `policy``update`
* `.delete``table` *DatabaseName*Имя_базы_данных`.`*TableName* , TableName `policy``update`

**Возвращает**

Команда удаляет объект политики обновления таблицы, а затем возвращает выходные данные соответствующей команды [. отобразить таблицу таблицы обновить политику](#show-update-policy) .

**Пример**

```kusto
.delete table DerivedTableX policy update 
```