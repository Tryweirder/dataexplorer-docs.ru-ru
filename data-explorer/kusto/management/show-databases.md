---
title: базы данных .show - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описаны базы данных .show в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1827c3ea20d984c6846ef9feb603398020efe275
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610187"
---
# <a name="show-databases"></a>базы данных .show

Возвращает таблицу, в которой каждая запись соответствует базе данных в кластере, к которой пользователь имеет доступ.

Чтобы увидеть возврат таблицы, показывающей [.show database](show-database.md)свойства базы данных контекста, см.

**Синтаксис**

`.show` `databases`

**Схема вывода**

|Имя столбца       |Тип столбца|Описание                                                                  |
|------------------|-----------|-----------------------------------------------------------------------------|
|имя_базы_данных      |`string`   |Название базы данных, прикрепленное к кластеру.                         |
|УстойчивоехранениеХранение |`string`   |Постоянное хранение "корень" базы данных. (Только для внутреннего использования.)      |
|Версия           |`string`   |Версия базы данных. (Только для внутреннего использования.)                        |
|IsCurrent         |`bool`     |Является ли эта база данных контекстом базы данных запроса.                |
|База данныхAccessMode|`string`   |Один `ReadWrite`из `ReadOnly` `ReadOnlyFollowing`, `ReadWriteEphemeral`, или .|
|PrettyName        |`string`   |Красивое название базы данных, если таковые имеется.                                    |
|ЗарезервированОСлот1     |`bool`     |Зарезервировано. (Только для внутреннего использования.)                                           |
|DatabaseId;        |`guid`     |Глобально уникальный идентификатор для базы данных. (Только для внутреннего использования.)      |
