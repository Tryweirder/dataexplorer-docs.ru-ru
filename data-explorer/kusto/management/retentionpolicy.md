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
ms.openlocfilehash: 7c9bc193e739011a5f91d9bd5d4d8746a7ce2591
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780564"
---
# <a name="retention-policy"></a>Политика хранения

Политика хранения управляет механизмом, который автоматически удаляет данные из таблиц. Полезно удалить данные, которые постоянно передаются в таблицу, и релевантность на основе возраста. Например, политику можно использовать для таблицы, содержащей события диагностики, которые могут стать неинтересными через две недели.

Политику хранения можно настроить для конкретной таблицы или для всей базы данных.
Затем политика применяется ко всем таблицам в базе данных, которые не переопределяют ее.

Настройка политики хранения важна для кластеров, которые постоянно принимают данные, что приведет к ограничению затрат.

Данные, не входящие в политику хранения, могут быть удалены. Kusto не гарантирует, когда происходит удаление. Данные могут быть "устаревшими" даже при активации политики хранения.

Политика хранения чаще всего устанавливается для ограничения срока хранения данных с момента приема.
Дополнительные сведения см. в разделе [софтделетепериод](#the-policy-object).

> [!NOTE]
> * Неточное время удаления. Система гарантирует, что данные не будут удаляться до превышения лимита, но удаление не будет сразу следовать за этой точкой.
> * Период обратимого удаления, равный 0, можно задать как часть политики хранения на уровне таблицы, но не как часть политики хранения на уровне базы данных.
>   * Когда это будет сделано, полученные данные не будут зафиксированы в исходной таблице, что позволит избежать необходимости сохранять данные. В результате `Recoverability` можно задать только значение `Disabled` .
>   * Такая конфигурация полезна в основном при приеме данных в таблицу.
> [Политика обновления](updatepolicy.md) транзакций используется для преобразования и перенаправления выходных данных в другую таблицу.

## <a name="the-policy-object"></a>Объект политики

Политика хранения включает следующие свойства:

* **Софтделетепериод**:
    * Интервал времени, для которого гарантируется, что данные остаются доступными для запроса. Период измеряется начиная с момента приема данных.
    * По умолчанию — `100 years`.
    * При изменении периода обратимого удаления таблицы или базы данных новое значение применяется как к существующим, так и к новым данным.
* Возможность **восстановления**:
    * Восстановление данных (включено или отключено) после удаления данных.
    * По умолчанию — `Enabled`.
    * Если задано значение `Enabled` , данные будут восстановлены в течение 14 дней после обратимого удаления.

## <a name="control-commands"></a>Управляющие команды

* Используйте [. Отображение политики хранения](../management/retention-policy.md) для отображения текущей политики хранения для базы данных или таблицы.
* Для изменения текущей политики хранения базы данных или таблицы используйте [инструкцию. ALTER Policy retention](../management/retention-policy.md) .

## <a name="defaults"></a>Значения по умолчанию

По умолчанию при создании базы данных или таблицы политика хранения не определена. Как правило, база данных создается, после чего она немедленно получает политику хранения, установленную ее создателем в соответствии с известными требованиями.
При выполнении [команды показ](../management/retention-policy.md) для политики хранения базы данных или таблицы, для которой не задана политика, `Policy` отображается как `null` .

Политика хранения по умолчанию с указанными выше значениями по умолчанию может быть применена с помощью следующей команды.

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
```

Команда приводит к применению к базе данных или таблице следующего объекта политики.

```kusto
{
  "SoftDeletePeriod": "36500.00:00:00", "Recoverability":"Enabled"
}
```

Очистка политики хранения базы данных или таблицы может быть выполнена с помощью следующей команды.

```kusto
.delete database DatabaseName policy retention
.delete table TableName policy retention
```

## <a name="examples"></a>Примеры

Для кластера с базой данных `MyDatabase` с именем, таблицами `MyTable1` , `MyTable2` и `MySpecialTable` .

### <a name="soft-delete-period-of-seven-days-and-recoverability-disabled"></a>Период обратимого удаления за семь дней и отключение возможности восстановления

Задайте для всех таблиц в базе данных период обратимого удаления в семь дней и отключите восстановление.

* *Вариант 1 (рекомендуется)*: Задайте политику хранения на уровне базы данных и убедитесь, что политики уровня таблицы не заданы.

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
```

* *Вариант 2*. для каждой таблицы задайте политику хранения на уровне таблицы, указав период обратимого удаления в семь дней и отключающий возможность восстановления.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

### <a name="soft-delete-period-of-seven-days-and-recoverability-enabled"></a>Период обратимого удаления из семи дней и включения возможности восстановления

* Задайте для таблиц и значение " `MyTable1` `MyTable2` обратимое удаление" в течение семи дней и включения возможности восстановления.
* Установите `MySpecialTable` для параметра значение обратимого удаления в течение 14 дней, а возможность восстановления отключена.

* *Вариант 1 (рекомендуется)*: Задайте политику хранения на уровне базы данных и задайте политику хранения на уровне таблицы.

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *Вариант 2*. для каждой таблицы задайте политику хранения на уровне таблицы с соответствующим периодом обратимого удаления и возможностью восстановления.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

### <a name="soft-delete-period-of-seven-days-and-myspecialtable-keeps-its-data-indefinitely"></a>Период обратимого удаления из семи дней и `MySpecialTable` сохранение его данных в течение неограниченного времени

Задайте для таблиц и значение " `MyTable1` `MyTable2` обратимое удаление" в семь дней, чтобы `MySpecialTable` хранить данные в течение неограниченного времени.

* *Вариант 1*. Настройка политики хранения на уровне базы данных и установка политики хранения на уровне таблицы с периодом обратимого удаления в 100 годах, политика хранения по умолчанию для `MySpecialTable` .

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *Вариант 2*. для таблиц `MyTable1` и `MyTable2` установите политику хранения на уровне таблицы и убедитесь, что политика уровня базы данных и уровня таблицы для `MySpecialTable` не задана.

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *Вариант 3*. для таблиц `MyTable1` и `MyTable2` Задайте политику хранения на уровне таблицы. Для параметра таблица `MySpecialTable` Задайте политику хранения на уровне таблицы с периодом обратимого удаления, равным 100 годам, в политике хранения по умолчанию.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```
