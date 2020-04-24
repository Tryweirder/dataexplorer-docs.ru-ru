---
title: Внешние таблицы - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описаны внешние таблицы в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: f4a059ba4533ed91353e75b499e564e6dd06d591
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509390"
---
# <a name="external-tables"></a>Внешние таблицы

**Внешняя таблица** — это сущность схемы Kusto, которая ссылается на данные, хранящиеся за пределами базы данных Kusto.

Как и в [таблицах,](tables.md)внешняя таблица имеет четко определенную схему (упорядоченный список пар имени столбца и типа данных). В отличие от таблиц, данные хранятся и управляются за пределами кластера Kusto. Чаще всего данные хранятся в стандартном формате, таких как CSV, Паркет, Avro, и не попадает в Кушнер.

**Внешняя таблица** создается один раз (см. [Команды управления внешней таблицей)](../../management/externaltables.md)и может быть отсылана по названию с помощью [функции external_table().](../../query/externaltablefunction.md) 

**Примечания**

* Внешние имена таблиц чувствительны к случаям.
* Внешние имена таблиц не могут перекрываться с именами таблиц Kusto.
* Внешние имена таблиц следуют правилам [имен сущностей.](./entity-names.md)
* Максимальный лимит внешних таблиц в базе данных составляет 1000.
* Kusto поддерживает [экспорт данных во внешнюю таблицу,](../../management/data-export/export-data-to-an-external-table.md) а также [запрос внешних таблиц.](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)
