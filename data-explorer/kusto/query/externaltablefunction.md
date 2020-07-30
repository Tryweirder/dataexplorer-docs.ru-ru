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
ms.openlocfilehash: 4a3a1150996000742f5065df0eddc385074eaa48
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348094"
---
# <a name="external_table"></a>external_table()

Ссылается на внешнюю таблицу по имени.

```kusto
external_table('StormEvent')
```

## <a name="syntax"></a>Синтаксис

`external_table``(` *TableName* [ `,` *MappingName* ]`)`

## <a name="arguments"></a>Аргументы

* *TableName*: Имя запрашиваемой внешней таблицы.
  Должен быть строковым литералом, ссылающимся на внешнюю таблицу типа `blob` или `adl` . <!-- TODO: Document data formats supported -->

* *MappingName*: необязательное имя объекта сопоставления, который сопоставляет поля в фактических (внешних) сегментах данных со столбцами, выводимыми этой функцией.

**Примечания**

Дополнительные сведения о внешних таблицах см. в разделе [внешние таблицы](schema-entities/externaltables.md) .

См. также [команды для управления внешними таблицами](../management/externaltables.md).

`external_table`Функция имеет аналогичные ограничения, чем [Табличная](tablefunction.md) функция.