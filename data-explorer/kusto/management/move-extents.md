---
title: . Перемещение экстентов — Azure обозреватель данных
description: В этой статье описывается команда Move экстенты в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 994f7077b1583317320cc561b2d5a5ae1cbe829f
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060715"
---
# <a name="move-extents"></a>. Перемещение экстентов

Эта команда выполняется в контексте определенной базы данных. Он перемещает указанные экстенты из исходной таблицы в целевую таблицу.

Команде требуется [разрешение администратора таблицы](../management/access-control/role-based-authorization.md) для исходной и целевой таблиц.

> [!NOTE]
> Сегменты данных называются **экстентами** в Kusto, а все команды используют "экстент" или "экстенты" в качестве синонима.
> Дополнительные сведения о экстентах см. в статье [Общие сведения о экстентах (сегментах данных)](extents-overview.md).

## <a name="syntax"></a>Синтаксис

`.move`[ `async` ] `extents` `all` `from` `table` *SourceTableName* `to` `table` *DestinationTableName*

`.move`[ `async` ] `extents` `(` *GUID1* [ `,` *GUID2* ...] `)` `from` `table` *SourceTableName* `to` `table` *DestinationTableName* 

`.move`[ `async` ] `extents` `to` `table` *DestinationTableName*  <|  *запрос*

`async`(необязательно). Выполните команду асинхронно. 
   * Возвращается идентификатор операции (GUID).
   * Состояние операции можно отслеживать. Используйте команду [. показ операций](operations.md#show-operations) .
   * Результаты успешного выполнения можно получить. Используйте команду [. отобразить сведения о операции](operations.md#show-operation-details) .

Существует три способа указать экстенты для перемещения.
* Перемещение всех экстентов определенной таблицы.
* Явно укажите идентификаторы экстентов в исходной таблице.
* Укажите запрос, результаты которого указывают идентификаторы экстентов в исходных таблицах.

## <a name="restrictions"></a>Ограничения

* Исходная и целевая таблицы должны находиться в базе данных контекста.
* Все столбцы исходной таблицы должны существовать в целевой таблице с тем же именем и типом данных.

## <a name="specify-extents-with-a-query"></a>Указание экстентов с помощью запроса

```kusto
.move extents to table TableName <| ...query...
```

Экстенты указываются с помощью запроса Kusto, возвращающего набор записей со столбцом с именем *екстентид*.

## <a name="return-output-for-sync-execution"></a>Возвращаемые выходные данные (для выполнения синхронизации)

Выходной параметр |Type |Описание:
---|---|---
оригиналекстентид |string |Уникальный идентификатор (GUID) для исходного экстента в исходной таблице, перемещенный в целевую таблицу.
ресултекстентид |string |Уникальный идентификатор (GUID) экстента результатов, который был перемещен из исходной таблицы в целевую таблицу. При сбое — "Failed".
Сведения |string |Включает сведения об ошибке в случае сбоя операции.

## <a name="examples"></a>Примеры

### <a name="move-all-extents"></a>Переместить все экстенты 

Переместить все экстенты из таблицы `MyTable` в таблицу `MyOtherTable` :

```kusto
.move extents all from table MyTable to table MyOtherTable
```

### <a name="move-two-specific-extents"></a>Перемещение двух конкретных экстентов 

Перемещение двух конкретных экстентов (по их идентификаторам экстентов) из таблицы `MyTable` в таблицу `MyOtherTable` :

```kusto
.move extents (AE6CD250-BE62-4978-90F2-5CB7A10D16D7,399F9254-4751-49E3-8192-C1CA78020706) from table MyTable to table MyOtherTable
```

### <a name="move-all-extents-from-specific-tables"></a>Переместить все экстенты из указанных таблиц 

Переместить все экстенты из определенной тавлес ( `MyTable1` , `MyTable2` ) в таблицу `MyOtherTable` :

```kusto
.move extents to table MyOtherTable <| .show tables (MyTable1,MyTable2) extents
```

## <a name="sample-output"></a>Пример выходных данных

|оригиналекстентид |ресултекстентид| Сведения
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-B06B-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 
