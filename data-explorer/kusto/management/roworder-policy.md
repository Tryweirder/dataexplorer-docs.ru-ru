---
title: Политика Ровордер — Azure обозреватель данных
description: В этой статье описывается политика Ровордер в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: bf8844a72d2b4fc3d9dec4f24fdfa6ac36ee84d7
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967508"
---
# <a name="roworder-policy-command"></a>Команда политики ровордер

В этой статье описываются управляющие команды, используемые для создания и изменения [политики порядка строк](../management/roworderpolicy.md).

## <a name="show-roworder-policy"></a>Отображение политики Ровордер

```kusto
.show table <table_name> policy roworder

.show table * policy roworder
```

## <a name="delete-roworder-policy"></a>Удалить политику Ровордер

```kusto
.delete table <table_name> policy roworder
```

## <a name="alter-roworder-policy"></a>Изменение политики Ровордер

```kusto
.alter table <table_name> policy roworder (<row_order_policy>)

.alter tables (<table_name> [, ...]) policy roworder (<row_order_policy>)

.alter-merge table <table_name> policy roworder (<row_order_policy>)
```

**Примеры** 

В следующем примере устанавливается политика порядка строк для `TenantId` столбца (по возрастанию) в качестве первичного ключа, а в `Timestamp` столбце (по возрастанию) — в качестве вторичного ключа. Затем выполняется запрос политики.

```kusto
.alter table events policy roworder (TenantId asc, Timestamp desc)

.alter tables (events1, events2, events3) policy roworder (TenantId asc, Timestamp desc)

.show table events policy roworder 
```

|TableName|ровордерполици| 
|---|---|
|события|(TenantId ASC, timestamp DESC)|
