---
title: current_database () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается current_database () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b4e0458c78023d35e002910de4c5946260b43b4f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252541"
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