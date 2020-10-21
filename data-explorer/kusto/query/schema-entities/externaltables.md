---
title: Внешние таблицы — обозреватель данных Azure | Документация Майкрософт
description: В этой статье описываются внешние таблицы в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: a2c3580ee265e0c67003d67c7e5a68cdd9165191
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342677"
---
# <a name="external-tables"></a>Внешние таблицы

**Внешняя таблица** — это сущность схемы Kusto, которая ссылается на данные, хранящиеся за пределами базы данных Обозреватель данных Azure.

Как и в случае с [таблицами](tables.md), внешняя таблица имеет четко определенную схему (упорядоченный список пар «имя столбца» и «тип данных»). В отличие от таблиц, данные хранятся и управляются за пределами кластера. Чаще всего данные хранятся в определенном стандартном формате, таком как CSV, Parquet, Avro, и не принимаются Azure обозреватель данных.

**Внешняя таблица** создается один раз. Дополнительные сведения о создании внешних таблиц см. в следующих командах.
* [Команды для общего управления внешней таблицей](../../management/external-table-commands.md)
* [Создание и изменение внешних таблиц SQL](../../management/external-sql-tables.md)
* [Создание и изменение таблиц в службе хранилища Azure или Azure Data Lake](../../management/external-tables-azurestorage-azuredatalake.md)

На **внешнюю таблицу** можно ссылаться по имени с помощью функции [external_table ()](../../query/externaltablefunction.md) . 

**Примечания**

* Имена внешних таблиц:
   * С учетом регистра.
   * Не может перекрываться с именами таблиц Kusto.
   * Следуйте правилам для [имен сущностей](./entity-names.md).
* Максимальное ограничение внешних таблиц на базу данных — 1 000.
* Kusto поддерживает [Экспорт](../../management/data-export/export-data-to-an-external-table.md) и [непрерывный экспорт](../../management/data-export/continuous-data-export.md) во внешнюю таблицу и [запросы к внешним таблицам](../../../data-lake-query-data.md).
* [Очистка данных](../../concepts/data-purge.md) не применяется к внешним таблицам. Записи никогда не удаляются из внешних таблиц.