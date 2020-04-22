---
title: таблица .drop и таблицы .drop - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны таблицы .drop и таблицы .drop в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 5f3a488aba5a6785ceb6ad4a093c520ec0509e5e
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744756"
---
# <a name="drop-table-and-drop-tables"></a>таблица .drop и таблицы .drop

Удаляет таблицу (или несколько таблиц) из базы данных.

Требуется [разрешение на админ-аймин таблицы.](../management/access-control/role-based-authorization.md)

> [!NOTE]
> Команда `.drop` `table` только мягкая удаляет данные (т.е. данные не могут быть запрошены, но по-прежнему восстанавливается из постоянного хранения). Основные артефакты хранилища трудно удаляются `recoverability` в соответствии с свойством в [политике удержания,](../management/retentionpolicy.md) которая действовала на момент попавания данных в таблицу.

**Синтаксис**

`.drop``table` *ТаблицаИмя* `ifexists`

`.drop``tables` (*TableName1*, *TableName2*,..) (если не существующие)

> [!NOTE]
> Если `ifexists` указано, команда не потерпит неудачу, если есть несуществующая таблица.

**Пример**

```kusto
.drop table CustomersTable ifexists
.drop tables (ProductsTable, ContactsTable, PricesTable) ifexists
```

**Возвращает**

Эта команда возвращает список оставшихся таблиц в базе данных. 

| Выходной параметр | Тип   | Описание                             |
|------------------|--------|-----------------------------------------|
| TableName        | Строка | Имя таблицы.                  |
| имя_базы_данных     | Строка | База данных, к которой принадлежит таблица. |