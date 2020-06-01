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
ms.openlocfilehash: 2b03f4e0ef426b07f88d7c179caecca6e7dd3034
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257830"
---
# <a name="external-tables"></a>Внешние таблицы

**Внешняя таблица** — это сущность схемы Kusto, которая ссылается на данные, хранящиеся за пределами базы данных Kusto.

Как и в случае с [таблицами](tables.md), внешняя таблица имеет четко определенную схему (упорядоченный список пар «имя столбца» и «тип данных»). В отличие от таблиц, данные хранятся и управляются вне кластера Kusto. Чаще всего данные хранятся в определенном стандартном формате, например CSV, Parquet, Avro и не принимаются Kusto.

**Внешняя таблица** создается один раз (см. [раздел внешние таблицы общие команды управления](../../management/externaltables.md), [Создание и изменение внешних таблиц SQL](../../management/external-sql-tables.md), [Создание и изменение таблиц в службе хранилища Azure или Azure Data Lake](../../management/external-tables-azurestorage-azuredatalake.md)) и на них можно ссылаться по имени с помощью функции [external_table ()](../../query/externaltablefunction.md) . 

**Примечания**

* В именах внешних таблиц учитывается регистр.
* Имена внешних таблиц не могут пересекаться с именами таблиц Kusto.
* Имена внешних таблиц соответствуют правилам для [имен сущностей](./entity-names.md).
* Максимальное ограничение внешних таблиц на базу данных — 1 000.
* Kusto поддерживает [Экспорт](../../management/data-export/export-data-to-an-external-table.md) и [непрерывный экспорт](../../management/data-export/continuous-data-export.md) во внешнюю таблицу, а также [запросы к внешним таблицам](../../../data-lake-query-data.md).
    * [Очистка данных](../../concepts/data-purge.md) не применяется к внешним таблицам. Записи никогда не удаляются из внешних таблиц.
