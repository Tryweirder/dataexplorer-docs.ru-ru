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
ms.openlocfilehash: 5ec069979d41a7c750c140ad84ef0db4ba5638a4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245070"
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

## <a name="next-steps"></a>Дальнейшие действия

* [Команды для общего управления внешней таблицей](../management/externaltables.md)
* [Создание и изменение внешних таблиц в службе хранилища Azure или Azure Data Lake](../management/external-tables-azurestorage-azuredatalake.md)
* [Создание и изменение внешних таблиц SQL](../management/external-sql-tables.md)
