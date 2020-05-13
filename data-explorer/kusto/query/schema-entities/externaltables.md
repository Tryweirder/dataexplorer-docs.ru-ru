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
ms.openlocfilehash: cfcf8a18bac1f6369b75538f2172fe8f25cb9660
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372944"
---
# <a name="external-tables"></a>Внешние таблицы

**Внешняя таблица** — это сущность схемы Kusto, которая ссылается на данные, хранящиеся за пределами базы данных Kusto.

Как и в случае с [таблицами](tables.md), внешняя таблица имеет четко определенную схему (упорядоченный список пар «имя столбца» и «тип данных»). В отличие от таблиц, данные хранятся и управляются вне кластера Kusto. Чаще всего данные хранятся в определенном стандартном формате, например CSV, Parquet, Avro и не принимаются Kusto.

**Внешняя таблица** создается один раз (см. раздел [команды управления внешней таблицы](../../management/externaltables.md)), и на нее можно ссылаться по имени с помощью функции [external_table ()](../../query/externaltablefunction.md) . 

**Примечания**

* В именах внешних таблиц учитывается регистр.
* Имена внешних таблиц не могут пересекаться с именами таблиц Kusto.
* Имена внешних таблиц соответствуют правилам для [имен сущностей](./entity-names.md).
* Максимальное ограничение внешних таблиц на базу данных — 1 000.
* Kusto поддерживает [Экспорт данных во внешнюю таблицу](../../management/data-export/export-data-to-an-external-table.md) , а также [запросы к внешним таблицам](../../../data-lake-query-data.md).
