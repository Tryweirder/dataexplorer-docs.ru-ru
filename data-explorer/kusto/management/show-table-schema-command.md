---
title: схема таблицы .show - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описывается схема таблицы .show в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 9223baa0242cc936b25a7d3293d102cb3966ed53
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519777"
---
# <a name="show-table-schema"></a>схема таблицы .шоу

Получает схему для использования в командах создания/изменения и дополнительных метаданных таблицы.

Требуется [разрешение пользователя базы данных.](../management/access-control/role-based-authorization.md)

```
.show table TableName cslschema 
```
| Выходной параметр | Тип   | Описание                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | Строка | Имя таблицы.                                    |
| схема           | Строка | Схема таблицы, которая должна использоваться для создания/изменения таблицы |
| имя_базы_данных     | Строка | База данных, к которой принадлежит таблица                   |
| Папка           | Строка | Папка таблицы                                            |
| DocString        | Строка | Документ таблицы                                         |


## <a name="show-table-schema-as-json"></a>схема таблицы показа как JSON

Получает схему в формате JSON и дополнительные метаданные таблицы.

Требуется [разрешение пользователя базы данных.](../management/access-control/role-based-authorization.md)

```
.show table TableName schema as JSON
```

| Выходной параметр | Тип   | Описание                             |
|------------------|--------|-----------------------------------------|
| TableName        | Строка | Имя таблицы.                   |
| схема           | Строка | Схема таблицы в формате JSON         |
| имя_базы_данных     | Строка | База данных, к которой принадлежит таблица |
| Папка           | Строка | Папка таблицы                          |
| DocString        | Строка | Документ таблицы                       |