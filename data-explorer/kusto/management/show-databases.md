---
title: . отображение баз данных в Azure обозреватель данных
description: В этой статье описывается, как показывать базы данных в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e5ff64db05bb85d88ed3da3347ea95cf02b0234b
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818529"
---
# <a name="show-databases"></a>.show databases

Возвращает таблицу, в которой каждая запись соответствует базе данных в кластере, к которой у пользователя есть доступ.

Таблицу, в которой показаны свойства контекстной базы данных, см. в разделе [`.show database`](show-database.md) .

**Синтаксис**

`.show` `databases`

**Схема вывода**

|Имя столбца       |Тип столбца|Описание                                                                  |
|------------------|-----------|-----------------------------------------------------------------------------|
|имя_базы_данных      |`string`   |Имя базы данных, присоединенной к кластеру                    |
|персистентстораже |`string`   |Корневой каталог постоянного хранилища базы данных. Только для внутреннего применения          |
|Version           |`string`   |Версия базы данных. Только для внутреннего применения                       |
|IsCurrent         |`bool`     |Является ли эта база данных контекстом базы данных запроса                    |
|датабасеакцессмоде|`string`   |Один из `ReadWrite` , `ReadOnly` , `ReadOnlyFollowing` или`ReadWriteEphemeral`    |
|преттинаме        |`string`   |Имя базы данных, если таковое имеется                        |
|ReservedSlot1     |`bool`     |Зарезервировано. Только для внутреннего применения              |
|DatabaseId;        |`guid`     |Глобальный уникальный идентификатор базы данных. Только для внутреннего применения          |
