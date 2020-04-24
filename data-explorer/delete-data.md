---
title: Удаление данных из обозревателя данных Azure
description: В этой статье описаны сценарии удаления в Azure Data Explorer, включая чистку, удаление степени и удаление на основе удержания.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: efbea2f9b8502f2521f2376f4aaf57902fa4e302
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498465"
---
# <a name="delete-data-from-azure-data-explorer"></a>Удаление данных из обозревателя данных Azure

Azure Data Explorer поддерживает различные сценарии удаления, описанные в этой статье. 

## <a name="delete-data-using-the-retention-policy"></a>Удаление данных с помощью политики удержания

Azure Data Explorer автоматически удаляет данные на основе [политики удержания.](kusto/management/retentionpolicy.md) Этот метод является наиболее эффективным и беспереданный способом удаляния данных. Установите политику удержания на уровне базы данных или таблицы.

Рассмотрим базу данных или таблицу со сроком хранения в 90 дней. Если требуется всего 60 дней данных, удалите старые данные следующим образом:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Удаление данных путем удаления степени

[Extent (осколок данных)](kusto/management/extents-overview.md) — это внутренняя структура, в которой хранятся данные. Каждая степень может вместить до миллионов записей. Масштабы могут быть удалены индивидуально или как группа с использованием [команд drop extent (ы)](kusto/management/extents-commands.md#drop-extents). 

### <a name="examples"></a>Примеры

Вы можете удалить все строки в таблице или только в определенной степени.

* Удалите все строки в таблице:

    ```kusto
    .drop extents from TestTable
    ```

* Удалить определенный упор:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Удаление отдельных строк с помощью чистки

[Очистка данных](kusto/concepts/data-purge.md) может быть использована для удаляния строк отдельных лиц. Удаление не является немедленным и требует значительных системных ресурсов. Таким образом, рекомендуется только для сценариев соответствия.  

