---
title: Политика RowOrder - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается политика RowOrder в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: cda4c9a6017071878832fab376a0376d250f3ed6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520253"
---
# <a name="roworder-policy"></a>Политика RowOrder

В этой статье описаны команды управления, используемые для создания и изменения [политики порядка строк.](../management/roworderpolicy.md)

## <a name="show-roworder-policy"></a>Показать политику RowOrder

```kusto
.show table <table_name> policy roworder

.show table * policy roworder
```

## <a name="delete-roworder-policy"></a>Удаление политики RowOrder

```kusto
.delete table <table_name> policy roworder
```

## <a name="alter-roworder-policy"></a>Политика Alter RowOrder

```kusto
.alter table <table_name> policy roworder (<row_order_policy>)

.alter tables (<table_name> [, ...]) policy roworder (<row_order_policy>)

.alter-merge table <table_name> policy roworder (<row_order_policy>)
```

**Примеры**

Следующий пример устанавливает, что политика `TenantId` порядка строк должна быть на столбце `Timestamp` (восходящем) в качестве основного ключа, а на столбце (восходящем) в качестве вторичного ключа; затем он запрашивает политику:

```kusto
.alter table events policy roworder (TenantId asc, Timestamp desc)

.alter tables (events1, events2, events3) policy roworder (TenantId asc, Timestamp desc)

.show table events policy roworder 
```

|TableName|RowOrderПолитика| 
|---|---|
|события|(TenantId asc, Timestamp desc)| 