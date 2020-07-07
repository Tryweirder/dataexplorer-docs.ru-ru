---
title: Управление таблицами в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается управление таблицами в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/27/2020
ms.openlocfilehash: 4de0e749ad47b8f2e3f2c0f26d5d18466efaff97
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967304"
---
# <a name="tables-management"></a>Управление таблицами

В этом разделе обсуждается жизненный цикл таблиц и связанных команд управления, которые полезны для изучения, создания и изменения таблиц.

Выберите ссылки в таблице ниже, чтобы получить дополнительные сведения о них.

| Команды                                                                                                                 | Операция                       |
|--------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| [`.alter table docstring`](alter-table-docstring-command.md), [`.alter table folder`](alter-table-folder-command.md)                                                                                                                                                                                                   | Управление свойствами табличного представления |
| [`.create ingestion mapping`](create-ingestion-mapping-command.md), [`.show ingestion mappings`](show-ingestion-mapping-command.md), [`.alter ingestion mapping`](alter-ingestion-mapping-command.md), [`.drop ingestion mapping`](drop-ingestion-mapping-command.md)                                                                    | Управление сопоставлением приема        |
| [`.create tables`](create-tables-command.md), [`.create table`](create-table-command.md), [`.alter table`](alter-table-command.md), [`.alter-merge table`](alter-table-command.md), [`.drop tables`](drop-table-command.md), [`.drop table`](drop-table-command.md), [`.undo drop table`](undo-drop-table-command.md), [`.rename table`](rename-table-command.md) | Создание, изменение и удаление таблиц       |
| [`.show tables`](show-tables-command.md) [`.show table details`](show-table-details-command.md)[`.show table schema`](show-table-schema-command.md)                                                                                      | Перечисление таблиц в базе данных  |
| `.ingest`, `.set`, `.append`, `.set-or-append` (Дополнительные сведения см. в разделе [прием данных](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands)).)                                                                                                                                                                                      | Прием данных в таблицу     |

## <a name="crud-naming-conventions-for-tables"></a>Соглашения об именовании CRUD для таблиц 
(См. полные сведения в разделах, связанных с таблицей выше.)
 
| Синтаксис команды                             | Семантика                                                                                                             |
|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| `.create entityType entityName ...`        | Если сущность с таким типом и именем существует, возвращает сущность. В противном случае создайте сущность.                          |
| `.create-merge entityType entityName...`   | Если сущность с таким типом и именем существует, объедините имеющуюся сущность с указанной сущностью. В противном случае создайте сущность. |
| `.alter entityType entityName ...`         | Если сущность с таким типом и именем не существует, то возникает ошибка. В противном случае замените его указанной сущностью.            |
| `.alter-merge entityType entityName ...`   | Если сущность с таким типом и именем не существует, то возникает ошибка. В противном случае Объедините его с указанной сущностью.              |
| `.drop entityType entityName ...`          | Если сущность с таким типом и именем не существует, то возникает ошибка. В противном случае удалите его.                                         |
| `.drop entityType entityName ifexists ...` | Если сущность с таким типом и именем не существует, возвращается значение. В противном случае удалите его.                                        |
 
> [!NOTE]
> "Merge" — это логическое слияние двух сущностей:
>
> * Если свойство определено для одной сущности, но не для другого, оно отображается с исходным значением в Объединенной сущности.
> * Если свойство определено для обеих сущностей и имеет одно и то же значение в обоих случаях, оно отображается один раз с этим значением в Объединенной сущности.
> * Если свойство определено для обеих сущностей, но имеет разные значения, возникает ошибка.