---
title: . Drop Table и. Drop-таблицы — Azure обозреватель данных
description: В этой статье описывается, как удалить таблицу и удалить таблицы в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 3e1eb57741302d34664f6cd8f256612a6e70bdd1
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264493"
---
# <a name="drop-table-and-drop-tables"></a>. Drop Table и. Drop, таблицы

Удаляет из базы данных таблицу или несколько таблиц.

Требуется [разрешение администратора таблицы](../management/access-control/role-based-authorization.md).

> [!NOTE]
> `.drop` `table` Команда только мягкая удаляет данные. То есть данные не могут быть запрошены, но по-прежнему можно восстановить из постоянного хранилища. Базовые артефакты хранилища жестко удаляются в соответствии со `recoverability` свойством в [политике хранения](../management/retentionpolicy.md) , которая действовала во время приема данных в таблице.

**Синтаксис**

`.drop``table` *TableName* [ `ifexists` ]

`.drop``tables`(*TableName1*, *TableName2*,..) [IFEXISTS]

> [!NOTE]
> Если `ifexists` указан параметр, команда не будет выполнена, если имеется несуществующая таблица.

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
