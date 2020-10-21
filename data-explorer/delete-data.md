---
title: Удаление данных из обозревателя данных Azure
description: В этой статье описываются сценарии удаления в Azure обозреватель данных, включая очистку, удаление экстентов и удаление на основе хранения.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/12/2020
ms.openlocfilehash: 962735c666fa94a0b86cfae8775f712ee42e6d33
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342847"
---
# <a name="delete-data-from-azure-data-explorer"></a>Удаление данных из обозревателя данных Azure

Azure обозреватель данных поддерживает различные сценарии удаления, описанные в этой статье. 

## <a name="delete-data-using-the-retention-policy"></a>Удаление данных с помощью политики хранения

Azure обозреватель данных автоматически удаляет данные на основе [политики хранения](kusto/management/retentionpolicy.md). Этот метод является наиболее эффективным и нетрудоемким способом удаления данных. Задайте политику хранения на уровне базы данных или таблицы.

Рассмотрим базу данных или таблицу со сроком хранения в 90 дней. Если требуются данные только 60 дней, удалите старые данные следующим образом:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Удаление данных путем удаления экстентов

[Экстент (сегмент данных)](kusto/management/extents-overview.md) — это внутренняя структура, в которой хранятся данные. Каждый экстент может содержать до миллиона записей. Экстенты можно удалять по отдельности или в виде группы с помощью [команд DROP экстента (s)](./kusto/management/drop-extents.md).

### <a name="examples"></a>Примеры

Можно удалить все строки в таблице или только конкретный экстент.

* Удалить все строки в таблице:

    ```kusto
    .drop extents from TestTable
    ```

* Удаление определенного экстента:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Удаление отдельных строк с помощью функции очистки

[Очистка данных](kusto/concepts/data-purge.md) может использоваться для удаления строк отдельных пользователей. Удаление не производится немедленно и требует значительных системных ресурсов. Таким образом, рекомендуется только для сценариев соответствия требованиям.