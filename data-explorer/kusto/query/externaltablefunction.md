---
title: external_table () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается external_table () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 13b244eb151d140e3626412188ac9bc9de242cc6
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802984"
---
# <a name="external_table"></a>external_table()

Ссылается на внешнюю таблицу по имени.

```kusto
external_table('StormEvent')
```

> [!NOTE]
> * `external_table`Функция имеет аналогичные ограничения, чем [Табличная](tablefunction.md) функция.
> * [Внешние таблицы](schema-entities/externaltables.md)
> * [Команды для управления внешними таблицами](../management/externaltables.md)

## <a name="syntax"></a>Синтаксис

`external_table``(` *TableName* [ `,` *MappingName* ]`)`

## <a name="arguments"></a>Аргументы

* *TableName*: Имя запрашиваемой внешней таблицы.
  Должен быть строковым литералом, ссылающимся на внешнюю таблицу типа `blob` или `adl` . <!-- TODO: Document data formats supported -->

* *MappingName*: необязательное имя объекта сопоставления, который сопоставляет поля в фактических (внешних) сегментах данных со столбцами, выводимыми этой функцией.
