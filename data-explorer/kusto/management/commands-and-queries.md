---
title: Управление командами и запросами - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается управление командами и запросами в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: f8c01709d69a0b439ce51b4782eb8f747db15d65
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521919"
---
# <a name="commands-and-queries-management"></a>Управление командами и запросами

## <a name="show-commands-and-queries"></a>.show команды-запросы 

`.show``commands-and-queries` возвращает таблицу с командами и запросами, достигаемыми в окончательном состоянии. Эти команды и запросы доступны для запроса в течение 30 дней.

Информация, представленная в выводе команды, аналогична информации, представленной [командами .show](commands.md) и [запросами .show,](queries.md)однако она по существу позволяет объединить оба набора результатов простым способом.

**Синтаксис**

`.show` `commands-and-queries`
 
**Вывод**
 
Схема вывода заключается в следующем:

| ColumnName               | ColumnType |
|--------------------------|------------|
| ClientActivityId         | строка     |
| CommandType              | строка     |
| текст                     | строка     |
| База данных                 | строка     |
| Startedon                | DATETIME   |
| LastUpdatedon            | DATETIME   |
| Duration                 | Интервал времени   |
| Состояние                    | строка     |
| FailureReason            | строка     |
| RootActivityId           | guid       |
| Пользователь                     | строка     |
| Приложение              | строка     |
| Основной                | строка     |
| КлиентЗапросСвойства  | Динамический    |
| TotalCpu                 | Интервал времени   |
| MemoryPeak               | long       |
| КэшСтатистика          | Динамический    |
| Сканнед-Квистстатистика | Динамический    |
| ResultSetСтатистика      | Динамический    |

Обратите внимание, что для `CommandType` запросов значение `Query`.