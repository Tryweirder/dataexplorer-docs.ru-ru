---
title: . отображение схемы таблицы в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается, как отобразить схему таблицы в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 899e54b46dd231db0bf1272c0eb1933dad474a47
ms.sourcegitcommit: 2ebd83369f247cf6dd91709f26e4ecd873489eaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83555021"
---
# <a name="show-table-schema"></a>.show table schema

Возвращает схему, используемую в командах CREATE/ALTER и дополнительные метаданные таблицы.

Требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

```kusto
.show table TableName cslschema 
```

| Выходной параметр | Type   | Описание                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | Строка | Имя таблицы.                                    |
| схема           | Строка | Схема таблицы должна использоваться для создания/изменения таблицы |
| имя_базы_данных     | Строка | База данных, к которой принадлежит таблица                   |
| Папка           | Строка | Папка таблицы                                            |
| DocString        | Строка | Docstring таблицы                                         |


## <a name="show-table-schema-as-json"></a>. отобразить схему таблицы как JSON

Возвращает схему в формате JSON и дополнительные метаданные таблицы.

Требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

```kusto
.show table TableName schema as json
```

| Выходной параметр | Type   | Описание                             |
|------------------|--------|-----------------------------------------|
| TableName        | Строка | Имя таблицы.                   |
| схема           | Строка | Схема таблицы в формате JSON         |
| имя_базы_данных     | Строка | База данных, к которой принадлежит таблица |
| Папка           | Строка | Папка таблицы                          |
| DocString        | Строка | Docstring таблицы                       |
