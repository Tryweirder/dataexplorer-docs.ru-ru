---
title: . ALTER-Merge Таблица — Azure обозреватель данных
description: В этой статье описывается команда. ALTER-Merge Table.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/08/2020
ms.openlocfilehash: cc4002d9af8b18841714ac9f91809fb18274782f
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84670520"
---
# <a name="alter-merge-table"></a>. ALTER-Merge таблица
 
Команда `.alter-merge table` делает следующее:

* Защита данных в существующих столбцах
* Добавляет новые столбцы, `docstring` и папку в существующую таблицу
* Должен выполняться в контексте определенной базы данных, в которой областью имен является таблица.
* Требуется [разрешение администратора таблицы](../management/access-control/role-based-authorization.md)

> [!WARNING]
> `.alter-merge`Неправильное использование команды может привести к утрате данных.

> [!TIP]
> `.alter-merge`Имеет аналог, `.alter` команду Table, имеющую аналогичные функции. Дополнительные сведения см. в разделе [. ALTER TABLE](../management/alter-table-command.md)

**Синтаксис**

`.alter-merge``table` *TableName* (*ColumnName*:*columnType*,...)  [ `with` `(` [ `docstring` `=` *Документация*] [ `,` `folder` `=` *имя_папки*] `)` ]

Укажите столбцы таблицы:
 * Столбцы, которые не существуют и которые вы указали, добавляются в конец существующей схемы.
 * Если переданная схема не содержит столбцов таблицы, то столбцы не удаляются.
 * Если указать существующий столбец с другим типом, команда завершится ошибкой.

> [!TIP]
> Используйте `.show table [TableName] cslschema` для получения схемы существующего столбца перед его изменением.

Как команда будет влиять на данные?
* Существующие данные физически не изменяются командой. Данные в удаленных столбцах не учитываются. Предполагается, что данные в новых столбцах имеют значение null.
* В зависимости от настройки кластера прием данных может изменить схему столбца таблицы даже без вмешательства пользователя. При внесении изменений в схему столбцов таблицы убедитесь, что при приеме не будут добавлены необходимые столбцы, которые затем будут удалены командой.

**Примеры**

```kusto
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```
 
