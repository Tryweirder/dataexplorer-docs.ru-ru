---
title: Политика обновления - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается политика обновления в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 95952a6f4e7a8c0d1a5b4207742e15873eb44c91
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519539"
---
# <a name="update-policy"></a>изменение политики;

[Политика обновления](updatepolicy.md) — объект политики на уровне таблицы для автоматического выполнения запроса и похищения его результатов при попадании данных в другую таблицу.

## <a name="show-update-policy"></a>Показать политику обновления

Эта команда возвращает политику обновления указанной таблицы или все `*` таблицы в базе данных по умолчанию, если используется в качестве имени таблицы.

**Синтаксис**

* `.show``table` *Название таблицы* `policy``update`
* `.show``table` *База данныхИмя*`.`*таблицыИмя* `policy``update`
* `.show` `table` `*` `policy` `update`

**Возвращает**

Эта команда возвращает таблицу с одной записью в таблице со следующими столбцов:

|Столбец    |Тип    |Описание                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|Имя объекта, на которое определена политика обновления,                                                                                                                |
|Политики  |`string`|Массив JSON с указанием всех политик обновления, определенных для сущности, отформатированных как [объект политики обновления](updatepolicy.md#the-update-policy-object)|

**Пример**

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |Политики                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|(ТестДБ). (ПроизводныйTableX)|«IsEnabled»: правда, «Источник»: «MyTableX», «Запрос»: «MyUpdateFunction()»,,«IsTransactional»: ложно,«PropagateIngestionСвойства»: ложно»|

## <a name="alter-update-policy"></a>Изменить политику обновления

Эта команда устанавливает политику обновления указанной таблицы.

**Синтаксис**

* `.alter``table` *Таблица* `policy` `update` *МассивОбновленияПолитикиОбъекты*
* `.alter``table` *База данныхИмя*`.`*таблицы* `policy` `update` *МассивобновленияПолитикиОбъектов*

*ArrayOfUpdatePolicyObjects* — это массив JSON, в который определены объекты политики обновления.

**Примечания**

1. Рекомендуется использовать сохраненную функцию для `Query` свойства объекта политики обновления.
   Это упрощает изменение только определения функции, а не всего объекта политики.

2. Следующие проверки выполняются в политике обновления, когда она `IsEnabled` устанавливается `true`(в случае, если установлено):
    1. `Source`: Таблица должна существовать в целевой базе данных.
    2. `Query`: 
        * Схема, определяемая схемой, должна соответствовать схеме целевой таблицы. 
        * Запрос должен ссылаться `source` на таблицу политики обновления. Определение запроса политики обновления, который *не* ссылается на источник, возможно путем установки `AllowUnreferencedSourceTable=true` в с свойствами (см. пример ниже), но, как правило, не рекомендуется из-за причин производительности (это означает, что для каждого приема в исходную таблицу *все* записи в другой таблице рассматриваются для выполнения политики обновления).
    3. Политика не приводит к циклу, создаваемому в цепочке политик обновления в целевой базе данных.
    4. В `IsTransactional` случае, `true`если `TableAdmin` установлен, разрешения `Source` проверяются против (исходная таблица), а также.
  
3. Убедитесь, что вы протестируете свою политику обновления / функцию для производительности, прежде чем применять ее для выполнения каждого приема в исходную таблицу - смотрите [здесь](updatepolicy.md#testing-an-update-policys-performance-impact).

**Возвращает**

Команда устанавливает объект политики обновления таблицы (переопределяет любую текущую политику, определенную, если она имеется), а затем возвращает вывод соответствующей команды [обновления таблицы TABLE.show TABLE.](#show-update-policy)

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

- При погламе в исходную таблицу (в данном случае) `MyTableX`в этой таблице создается 1 или более уровней (осколки данных).
- Объект, `Query` определяемый в объекте политики обновления (в данном случае), `MyUpdateFunction()`будет работать только по этим размерам и не будет работать по всей таблице.
  - Это "скопирование" выполняется внутренне и автоматически, он не `Query`должен обрабатываться при определении .
  - При попаченной таблице (в данном случае) `DerivedTableX`будут учитываться только вновь пропавшие записи (разные в каждой операции приема).


```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-table-policy-update"></a>обновление таблицы TABLE table .alter-merge

Эта команда изменяет политику обновления указанной таблицы.

**Синтаксис**

* `.alter-merge``table` *Таблица* `policy` `update` *МассивОбновленияПолитикиОбъекты*
* `.alter-merge``table` *База данныхИмя*`.`*таблицы* `policy` `update` *МассивобновленияПолитикиОбъектов*

*ArrayOfUpdatePolicyObjects* — это массив JSON, в который определены объекты политики обновления.

**Примечания**

1. Рекомендуется использовать сохраненные функции для массовой реализации свойства запроса объекта политики обновления. Это упрощает изменение только определения функции, а не всего объекта политики.

2. Те же проверки, выполняемые в `alter` политике обновления `alter-merge` в случае выполнения команды для команды.

**Возвращает**

Команда прикладывается к объекту политики обновления таблицы (переопределяет любую текущую политику, определенную, если таковой имеется), а затем возвращает вывод соответствующей команды [обновления таблицы TABLE.show TABLE.](#show-update-policy)

**Пример**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-table-policy-update"></a>.delete таблица TABLE обновление политики

Удаляет политику обновления указанной таблицы.

**Синтаксис**

* `.delete``table` *Название таблицы* `policy``update`
* `.delete``table` *База данныхИмя*`.`*таблицыИмя* `policy``update`

**Возвращает**

Команда удаляет объект политики обновления таблицы, а затем возвращает вывод соответствующей команды [обновления таблицы TABLE TABLE.](#show-update-policy)

**Пример**

```kusto
.delete table DerivedTableX policy update 
```