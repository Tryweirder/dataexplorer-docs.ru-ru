---
title: current_database () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны current_database() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c61717bbc8d202624b36088df5aed2ba3f3a8d2d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516751"
---
# <a name="current_database"></a>current_database()

Возвращает имя базы данных в области (база данных, против которую все объекты запроса разрешаются, если не указана другая база данных).

**Синтаксис**

`current_database()`

**Возвращает**

Название базы данных в области как `string`значение типа .

**Пример**

```kusto
print strcat("Database in scope: ", current_database())
```