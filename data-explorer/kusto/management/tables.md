---
title: Управление таблицами - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается управление таблицами в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/27/2020
ms.openlocfilehash: 7b7e4c5c7111354864aa939ece76be2ab0a8ac15
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519573"
---
# <a name="tables-management"></a>Управление столами

Эта тема обсуждает жизненный цикл таблиц и связанных команд управления.

Выберите ссылки в таблице ниже для получения дополнительной информации о них.

| Команды                                                                                                                 | Операция                       |
|--------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| [`.alter table docstring`](alter-table-docstring-command.md), [`.alter table folder`](alter-table-folder-command.md)                                                                                                                                                                                                   | Управление свойствами отображения таблицы |
| [`.create ingestion mapping`](create-ingestion-mapping-command.md), [`.show ingestion mappings`](show-ingestion-mapping-command.md), [`.alter ingestion mapping`](alter-ingestion-mapping-command.md), [`.drop ingestion mapping`](drop-ingestion-mapping-command.md)                                                                    | Управление картированием приема        |
| [`.create tables`](create-tables-command.md), [`.create table`](create-table-command.md), [`.alter table`](alter-table-command.md), [`.alter-merge table`](alter-table-command.md), [`.drop tables`](drop-table-command.md), [`.drop table`](drop-table-command.md), [`.undo drop table`](undo-drop-table-command.md), [`.rename table`](rename-table-command.md) | Создание/изменение/падение таблиц       |
| [`.show tables`](show-tables-command.md) [`.show table details`](show-table-details-command.md)[`.show table schema`](show-table-schema-command.md)                                                                                      | Перечисление таблиц в базе данных  |
| `.ingest`, `.set`, `.append`, `.set-or-append` (Дополнительные сведения см. в разделе [прием данных](./data-ingestion/index.md)).)                                                                                                                                                                                      | Проглатывание данных в таблицу     |

## <a name="crud-naming-conventions-for-tables"></a>Конвенции именования CRUD для таблиц 
(См. полную информацию в разделах, связанных с в таблице, выше.)
 
| Командный синтаксис                             | Семантика                                                                                                             |
|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| `.create entityType entityName ...`        | Если существует сущность такого типа и имени, возвращает сущность. В противном случае создайте сущность.                          |
| `.create-merge entityType entityName...`   | Если существует сущность этого типа и имени, объедините существующую сущность с указанным объектом. В противном случае создайте сущность. |
| `.alter entityType entityName ...`         | Если сущность такого типа и имени не существует, ошибка. В противном случае замените его указанным объектом.            |
| `.alter-merge entityType entityName ...`   | Если сущность такого типа и имени не существует, ошибка. В противном случае свяжете его с указанным объектом.              |
| `.drop entityType entityName ...`          | Если сущность такого типа и имени не существует, ошибка. В противном случае, падение его.                                         |
| `.drop entityType entityName ifexists ...` | Если сущность такого типа и имени не существует, вернитесь. В противном случае, падение его.                                        |
 
> [!NOTE]
> "Слияние" представляет собой логическое слияние двух сущностей:
>
> * Если свойство определено для одной сущности, но не для другого, оно отображается с его исходным значением в объединенном объекте.
> * Если свойство определено для обоих сущностей и имеет одинаковое значение в обоих, оно появляется один раз с этим значением в объединенной сущности.
> * Если свойство определено для обоих сущностей, но имеет разные значения, возникает ошибка.