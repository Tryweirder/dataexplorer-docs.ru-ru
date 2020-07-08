---
title: . Drop экстенты — Azure обозреватель данных
description: В этой статье описывается команда DROP экстентов в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: dfa462ca82cd5e94adff77b3893b3b02d60c6cdc
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060722"
---
# <a name="drop-extents"></a>. Перетащите экстенты

Удаляет экстенты из указанной базы данных или таблицы.

Эта команда имеет несколько вариантов: в одном случае удаляемые экстенты задаются запросом Kusto. В других вариантах экстенты указываются с помощью мини-языка, описанного ниже.

Требуется [разрешение администратора таблицы](../management/access-control/role-based-authorization.md) для каждой таблицы, которая содержит экстенты, возвращенные предоставленным запросом.

> [!NOTE]
> Сегменты данных называются **экстентами** в Kusto, а все команды используют "экстент" или "экстенты" в качестве синонима.
> Дополнительные сведения о экстентах см. в статье [Общие сведения о экстентах (сегментах данных)](extents-overview.md).

## <a name="drop-extents-with-a-query"></a>Перетаскивание экстентов с помощью запроса

Перетащите экстенты, указанные с помощью запроса Kusto.
Возвращается набор записей со столбцом с именем «Екстентид».

Если `whatif` используется, будет просто сообщать о них без фактического удаления.

### <a name="syntax"></a>Синтаксис

`.drop``extents`[ `whatif` ] <| *запрос*

## <a name="drop-a-specific-extent"></a>Удаление определенного экстента

Требуется [разрешение администратора таблицы](../management/access-control/role-based-authorization.md) , если указано имя таблицы.

Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md) , если не указано имя таблицы.

### <a name="syntax"></a>Синтаксис

`.drop``extent` *Екстентид* [ `from` *имя_таблицы*]

## <a name="drop-specific-multiple-extents"></a>Удалить определенные несколько экстентов

Требуется [разрешение администратора таблицы](../management/access-control/role-based-authorization.md) , если указано имя таблицы.

Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md) , если не указано имя таблицы.

### <a name="syntax"></a>Синтаксис

`.drop``extents` `(` *ExtentId1* `,` ... *Екстентидн* `)` [ `from` *TableName*]

## <a name="drop-extents-by-specified-properties"></a>Перетаскивание экстентов по указанным свойствам

Команда поддерживает режим эмуляции, который создает выходные данные, как если бы команда выполнялась, но без фактического выполнения. Используйте `.drop-pretend` вместо `.drop`.

Требуется [разрешение администратора таблицы](../management/access-control/role-based-authorization.md) , если указано имя таблицы.

Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md) , если не указано имя таблицы.

### <a name="syntax"></a>Синтаксис

`.drop``extents`[ `older` *N* ( `days`  |  `hours` )] `from` (*TableName*  |  `all` `tables` ) [ `trim` `by` ( `extentsize`  |  `datasize` ) *N* ( `MB`  |  `GB`  |  `bytes` )] [ `limit` *лимиткаунт*]

* `older`: Будут удалены только экстенты старше *N* дней/ч.
* `trim`: Операция обрезает данные в базе данных до тех пор, пока сумма экстентов не будет соответствовать требуемому размеру (MaxSize).
* `limit`: Операция будет применена к первым экстентам *лимиткаунт* .

## <a name="examples"></a>Примеры

### <a name="remove-all-extents-by-time-created"></a>Удалить все экстенты по времени создания

Удалить все экстенты, созданные более 10 дней назад, из всех таблиц в базе данных`MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

### <a name="remove-some-extents-by-time-created"></a>Удалить некоторые экстенты по времени создания

Удалить все экстенты в таблицах `Table1` , `Table2` время создания которых было более 10 дней назад

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

### <a name="emulation-mode-show-which-extents-would-be-removed-by-the-command"></a>Режим эмуляции: показывает, какие экстенты будут удалены командой

>[!NOTE]
>Параметр идентификатора экстента не применим для этой команды.

```kusto
.drop-pretend extents older 10 days from all tables
```

### <a name="remove-all-extents-from-testtable"></a>Удалить все экстенты из "Тесттабле"

```kusto
.drop extents from TestTable
```

## <a name="return-output"></a>Возврат выходных данных

|Выходной параметр |Type |Описание: 
|---|---|---
|екстентид |Строка |Екстентид, который был удален из-за команды
|TableName |Строка |Имя таблицы, где экстент находится в долговременном виде  
|CreatedOn |Дата и время |Отметка времени, содержащая сведения о том, когда экстент был изначально создан
 
## <a name="sample-output"></a>Пример выходных данных

|Идентификатор экстента |Имя таблицы |Создана 
|---|---|---
|43c6e03f-1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12:48:49.4298178
