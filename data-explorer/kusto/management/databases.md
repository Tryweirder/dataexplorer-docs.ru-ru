---
title: Управление базами данных в Azure обозреватель данных
description: В этой статье описывается управление базами данных в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 9dc2e1bf489123ddd66c9a203d74284e36eda53e
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320950"
---
# <a name="databases-management"></a>Управление базами данных

В этом разделе описаны следующие команды управления базой данных.

|Команда |Описание |
|--------|------------|
|[`.show databases`](show-databases.md) |Возвращает таблицу, в которой каждая запись соответствует базе данных в кластере, к которой имеет доступ пользователь.|
|[`.show database`](show-database.md) |Возвращает таблицу, показывающую свойства контекстной базы данных |
|[`.show cluster databases`](show-cluster-database.md) |Возвращает таблицу, в которой показаны все базы данных, подключенные к кластеру, к которым пользователь вызывает команду, имеет доступ |
|[`.alter database`](alter-database.md) |Изменяет очень (понятное) имя базы данных |
|[`.show database schema`](show-schema-database.md) |Возвращает плоский список структуры выбранных баз данных со всеми таблицами и столбцами в одной таблице или объекте JSON. |