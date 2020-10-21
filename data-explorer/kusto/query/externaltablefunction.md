---
title: external_table () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается external_table () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: b966dbd43d1f40842240eaebf7d4008450e1f746
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343204"
---
# <a name="external_table"></a>external_table()

Ссылается на [внешнюю таблицу](schema-entities/externaltables.md) по имени.

```kusto
external_table('StormEvent')
```

> [!NOTE]
> * `external_table`Функция имеет аналогичные ограничения, чем [Табличная](tablefunction.md) функция.
> * Стандартные [ограничения запросов](../concepts/querylimits.md) применяются и к запросам внешних таблиц.

## <a name="syntax"></a>Синтаксис

`external_table``(` *TableName* [ `,` *MappingName* ]`)`

## <a name="arguments"></a>Аргументы

* *TableName*: Имя запрашиваемой внешней таблицы.
  Должен быть строковым литералом, ссылающимся на внешнюю таблицу типа `blob` , `adl` или `sql` .

* *MappingName*: необязательное имя объекта сопоставления, который сопоставляет поля в фактических (внешних) сегментах данных со столбцами, выводимыми этой функцией.

## <a name="next-steps"></a>Дальнейшие шаги

* [Команды для общего управления внешней таблицей](../management/external-table-commands.md)
* [Создание и изменение внешних таблиц в службе хранилища Azure или Azure Data Lake](../management/external-tables-azurestorage-azuredatalake.md)
* [Создание и изменение внешних таблиц SQL](../management/external-sql-tables.md)