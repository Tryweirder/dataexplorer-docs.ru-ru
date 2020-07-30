---
title: current_database () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается current_database () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d68c35547c840cc1e16224c376e90dfabec296d7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348706"
---
# <a name="current_database"></a>current_database()

Возвращает имя базы данных в области (база данных, для которой разрешаются все сущности запроса, если не указана другая база данных).

## <a name="syntax"></a>Синтаксис

`current_database()`

## <a name="returns"></a>Результаты

Имя базы данных в области в виде значения типа `string` .

## <a name="example"></a>Пример

```kusto
print strcat("Database in scope: ", current_database())
```