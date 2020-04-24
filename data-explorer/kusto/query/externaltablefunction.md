---
title: external_table () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается external_table () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/21/2019
ms.openlocfilehash: 9fd03fb3c8452702c3db27a5e0466e8608c04eb9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515459"
---
# <a name="external_table"></a>external_table()

Ссылки на внешнюю таблицу по имени.

```kusto
external_table('StormEvent')
```

**Синтаксис**

`external_table``(` *Название таблицы* -`,` *ОтображениеИмя*`)`

**Аргументы**

* *TableName*: Название запрашиваемой внешней таблицы.
  Должно быть строка буквально ссылаясь на внешний стол рода `blob` или `adl`. <!-- TODO: Document data formats supported -->

* *MappingName*: Дополнительное имя объекта отображения, который отображает поля в фактических (внешних) осколков данных к выдавливам столбцов этой функции.

**Примечания**

Дополнительные сведения о внешних таблицах прознакомитесь с [внешними таблицами.](schema-entities/externaltables.md)

Смотрите также [команды для управления внешними таблицами.](../management/externaltables.md)

Функция `external_table` имеет те же ограничения, что и функция [таблицы.](tablefunction.md)