---
title: Управление командами и запросами — Azure обозреватель данных
description: В этой статье описываются команды и управление запросами в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/19/2019
ms.openlocfilehash: 222d04939560342bd849c15f249b1a8a582316a2
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321035"
---
# <a name="commands-and-queries-management"></a>Управление командами и запросами

## <a name="show-commands-and-queries"></a>. Отображение команд и запросов 

`.show``commands-and-queries`возвращает таблицу с командами администрирования и запросами, которые достигают конечного состояния. Эти команды и запросы доступны в течение 30 дней.

Информация, представленная в выходных данных команды, аналогична [ `.show` командам](commands.md) и [ `.show` запросам](queries.md), однако она позволяет легко объединять оба результирующих набора.

**Синтаксис**

`.show` `commands-and-queries`
 
**Выходные данные**
 
Схема вывода выглядит следующим образом:

| ColumnName               | ColumnType |
|--------------------------|------------|
| клиентактивитид         | строка     |
| CommandType              | строка     |
| Текст                     | строка     |
| База данных                 | строка     |
| стартедон                | DATETIME   |
| ластупдатедон            | DATETIME   |
| Длительность                 | Интервал времени   |
| Состояние                    | строка     |
| FailureReason            | строка     |
| RootActivityId           | guid       |
| Пользователь                     | строка     |
| Приложение              | строка     |
| Основной                | строка     |
| клиентрекуестпропертиес  | Динамический    |
| тоталкпу                 | Интервал времени   |
| меморипеак               | long       |
| качестатистикс          | Динамический    |
| сканнедекстентсстатистикс | Динамический    |
| ресултсетстатистикс      | Динамический    |

> [!NOTE]
> Для запросов значение `CommandType` равно `Query` .
