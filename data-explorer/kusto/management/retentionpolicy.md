---
title: Политика хранения Kusto управляет способом удаления данных — Azure обозреватель данных
description: В этой статье описывается политика хранения в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 3d854262a2a446f983f60c49a5c0ca02f6aa2ffe
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011379"
---
# <a name="retention-policy"></a>Политика хранения

Политика хранения управляет механизмом автоматического удаления данных из таблиц.
Такое удаление обычно полезно для данных, которые последовательно передаются в таблицу, релевантность которой зависит от возраста. Например, политику хранения можно использовать для таблицы, содержащей события диагностики, которые могут стать неинтересными через две недели.

Политику хранения можно настроить для конкретной таблицы или для всей базы данных (в этом случае она применяется ко всем таблицам в базе данных, которые не переопределяют политику).

Настройка политики хранения важна для кластеров, которые постоянно принимают данные, что приведет к ограничению затрат.

Данные, не входящие в политику хранения, могут быть удалены. Kusto не гарантирует, когда происходит удаление (так что данные могут быть "устаревшими" даже при активации политики хранения).

Политика хранения чаще всего устанавливается для ограничения срока хранения данных с момента приема (см. [софтделетепериод](#the-policy-object)ниже).

> [!NOTE]
> * Неточное время удаления. Система гарантирует, что данные не будут удалены до превышения лимита, но удаление сразу же после этого момента.
> * Период обратимого удаления, равный 0, можно задать как часть политики хранения на уровне таблицы (но не как часть политики хранения на уровне базы данных).
>   * Когда это будет сделано, полученные данные не будут зафиксированы в исходной таблице, что позволит избежать необходимости сохранять данные. Следовательно, `Recoverability` можно задать только значение `Disabled` . 
>   * Такая конфигурация полезна в основном при приеме данных в таблицу.
>   [Политика обновления](updatepolicy.md) транзакций используется для преобразования и перенаправления выходных данных в другую таблицу.

## <a name="the-policy-object"></a>Объект политики

Политика хранения включает следующие свойства:

* **Софтделетепериод**:
    * Период времени, для которого гарантируется, что данные остаются доступными для запроса, измеряемый с момента приема.
    * По умолчанию имеет значение `100 years`.
    * При изменении периода обратимого удаления таблицы или базы данных новое значение применяется как к существующим, так и к новым данным.
* Возможность **восстановления**:
    * Восстановление данных (включено или отключено) после удаления данных
    * По умолчанию — `Enabled`.
    * Если задано значение `Enabled` , данные будут восстановлены в течение 14 дней после обратимого удаления.

## <a name="control-commands"></a>Управляющие команды

* Используйте [. отображать политику хранения](../management/retention-policy.md) для отображения текущей политики хранения для базы данных или таблицы.
* Для изменения текущей политики хранения базы данных или таблицы используйте [инструкцию. ALTER Policy retention](../management/retention-policy.md) .

## <a name="defaults"></a>Умолчания;

По умолчанию при создании базы данных или таблицы политика хранения не определена.
В общих случаях база данных создается, и после этого политика хранения немедленно задается ее создателем в соответствии с известными требованиями.
При выполнении [команды показ](../management/retention-policy.md) для политики хранения базы данных или таблицы, для которой не задана политика, `Policy` отображается как `null` .

Политику хранения по умолчанию (с указанными выше значениями по умолчанию) можно применить с помощью следующей команды:

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
```

Эти результаты имеют следующий объект политики, применяемый к базе данных или таблице:

```kusto
{
  "SoftDeletePeriod": "36500.00:00:00", "Recoverability":"Enabled"
}
```

Очистка политики хранения базы данных или таблицы может быть выполнена с помощью следующей команды:

```kusto
.delete database DatabaseName policy retention
.delete table TableName policy retention
```

## <a name="examples"></a>Примеры

При наличии в кластере базы данных `MyDatabase` с именем, с таблицами `MyTable1` `MyTable2` и`MySpecialTable`

**1. Установка для всех таблиц в базе данных периода обратимого удаления в 7 дней и отключения возможности восстановления**:

* *Вариант 1 (рекомендуется)*. Задайте для политики хранения на уровне базы данных период обратимого удаления в семь дней и возможность восстановления, а также убедитесь, что политики уровня таблицы не заданы.

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
```

* *Вариант 2*. для каждой таблицы задайте политику хранения на уровне таблицы с периодом обратимого удаления в семь дней и отключите возможность восстановления.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

**2. Установка в таблицах `MyTable1` `MyTable2` периода обратимого удаления в течение 7 дней и включения возможности восстановления, а для параметра режим `MySpecialTable` обратимого удаления — 14 дней, а возможность восстановления отключена**.

* *Вариант 1 (рекомендуется)*. Задайте политику хранения на уровне базы данных с периодом обратимого удаления в семь дней и возможностью восстановления, а также установите политику хранения на уровне таблицы с возможностью обратимого удаления в течение 14 дней, а возможность восстановления отключена для `MySpecialTable` .

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *Вариант 2*. для каждой таблицы задайте политику хранения на уровне таблицы с нужным периодом обратимого удаления и возможностью восстановления.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

**3. Настройка таблиц `MyTable1` `MyTable2` для использования периода обратимого удаления 7 дней и `MySpecialTable` сохранения данных в течение неограниченного времени**:

* *Вариант 1*. Задайте для политики хранения на уровне базы данных период обратимого удаления в семь дней и задайте для политики хранения на уровне таблицы период обратимого удаления в 100 лет (политика хранения по умолчанию) для `MySpecialTable` .

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *Вариант 2*. для таблиц `MyTable1` `MyTable2` установите политику хранения на уровне таблицы с требуемым периодом обратимого удаления в семь дней и убедитесь, что политика уровня базы данных и уровня таблицы для `MySpecialTable` не задана.

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *Вариант 3*. для таблиц `MyTable1` `MyTable2` установите политику хранения на уровне таблицы с требуемым периодом обратимого удаления в семь дней. Для параметра таблица `MySpecialTable` установите политику хранения на уровне таблицы с периодом обратимого удаления в 100 лет (политика хранения по умолчанию).

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```
