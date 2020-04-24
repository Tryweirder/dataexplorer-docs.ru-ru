---
title: Политика удержания - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается политика удержания в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 81e08b6e007a6e3c669e7138e1d36ae1e701d442
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520321"
---
# <a name="retention-policy"></a>Политика хранения

Политика удержания контролирует механизм, с помощью которого данные автоматически удаляются из таблиц.
Такое удаление обычно полезно для данных, которые постоянно всасываются в таблицу, актуальность которой является возрастной. Например, политика удержания может быть использована для таблицы, в ней содержится события диагностики, которые могут стать непересекающимися через две недели.

Политика удержания может быть настроена для конкретной таблицы или для всей базы данных (в этом случае она применяется ко всем таблицам в базе данных, которые не переопределяют политику).

Настройка политики удержания важна для кластеров, которые постоянно просажают данные, что ограничит затраты.

Данные, которые "вне" политики удержания, могут быть удалены. Kusto не гарантирует, когда удаление происходит (так что данные могут "задерживаться", даже если политика удержания была запущена).

Политика удержания чаще всего устанавливается для ограничения возраста данных с момента приема (см. [SoftDeletePeriod](#the-policy-object), ниже).

> [!NOTE]
> * Время удаления неточно. Система гарантирует, что данные не будут удалены до превышения лимита, но удаление не является немедленной после этого момента.
> * Период «мягкого удаления 0» может быть установлен как часть политики удержания на уровне таблицы (но не как часть политики удержания уровня базы данных).
>   * Когда это будет сделано, просаженные данные не будут засечены в исходной таблице, избегая необходимости сохранять данные.
>   * Такая конфигурация полезна главным образом при попадании данных в таблицу.
>   Для преобразования и перенаправления вывода в другую таблицу используется [политика обновления](updatepolicy.md) транзакций.

## <a name="the-policy-object"></a>Объект политики

Политика удержания включает следующие свойства:

* **SoftDeletePeriod**:
    * Временной промежуток, за который гарантируется, что данные хранятся в наличии для запроса, измеряется с момента их попавания.
    * По умолчанию равен `100 years`.
    * При изменении периода мягкого удаления таблицы или базы данных новое значение применяется как к существующим, так и к новым данным.
* **Восстановление:**
    * Восстановление данных (включено/отключено) после удаления данных
    * По умолчанию — `enabled`.
    * Если `enabled`установлено, данные будут восстановлены в течение 14 дней после удаления

## <a name="control-commands"></a>Управляющие команды

* Используйте [удержание политики .show,](../management/retention-policy.md) чтобы показать текущую политику удержания для базы данных или таблицы.
* Используйте [удержание политики .alter](../management/retention-policy.md) для изменения текущей политики удержания базы данных или таблицы.

## <a name="defaults"></a>Умолчания;

По умолчанию, когда создается база данных или таблица, она не определяется политикой удержания.
В обычных случаях база данных создается, а затем сразу же устанавливает свою политику удержания, установленную ее создателем в соответствии с известными требованиями.
При запуске [командной команды шоу](../management/retention-policy.md) для политики удержания базы данных или таблицы, у которой не было набора политики, `Policy` отображается как `null`.

Политика удержания по умолчанию (с упомянутыми выше значениями по умолчанию) может быть применена с помощью следующей команды:

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
```

Эти результаты со следующим объектом политики, применимыми к базе данных или таблице:

```kusto
{
  "SoftDeletePeriod": "36500.00:00:00", "Recoverability":"Enabled"
}
```

Очистка политики удержания базы данных или таблицы может быть выполнена с помощью следующей команды:

```kusto
.delete database DatabaseName policy retention
.delete table TableName policy retention
```

## <a name="examples"></a>Примеры

Учитывая, что ваш `MyDatabase`кластер `MyTable1`имеет `MyTable2` базу данных с именем, со таблицами, и`MySpecialTable`

**1. Установка всех таблиц в базе данных с возможностью мягкого удаления в 7 дней и отключение восстановления:**

* *Вариант 1 (Рекомендуется)*: Установите политику удержания на уровне базы данных с периодом мягкого удаления в семь дней и отключением восстановления и проверьте наличие политики на уровне таблицы.

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
```

* *Вариант 2*: Для каждой таблицы установите политику удержания на уровне таблицы с периодом мягкого удаления в семь дней и отключенной возможностью восстановления.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

**2. `MyTable1`Настройка `MyTable2` таблиц , чтобы иметь период мягкого удаления 7 `MySpecialTable` дней и восстановление включен, и установить, чтобы иметь период мягкого удаления 14 дней и восстановление отключено:**

* *Вариант 1 (Рекомендуется)*: Установите политику удержания на уровне базы данных с периодом мягкого удаления в семь дней и включенной возможностью `MySpecialTable`восстановления, и установите политику удержания на уровне таблицы с периодом мягкого удаления в 14 дней и возможность восстановления отключена для .

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *Вариант 2*: Для каждой таблицы установите политику удержания на уровне таблицы с желаемым периодом мягкого удаления и возможностью восстановления.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

**3. `MyTable1`Настройка `MyTable2` таблиц , чтобы иметь период мягкого `MySpecialTable` удаления 7 дней, и держать свои данные на неопределенный срок:**

* *Вариант 1*: Установите политику удержания на уровне базы данных с периодом мягкого удаления в семь дней и установите политику `MySpecialTable`удержания на уровне таблицы с периодом мягкого удаления в 100 лет (политика удержания по умолчанию) для .

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *Вариант 2*: `MyTable1` `MyTable2`Для таблиц , , установите политику удержания на уровне таблицы с желаемым периодом мягкого удаления в семь дней, и убедитесь, что политика `MySpecialTable` уровня базы данных и таблицы не установлена.

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *Вариант 3*: `MyTable1` `MyTable2`Для таблиц , , установите политику удержания на уровне таблицы с желаемым периодом мягкого удаления в семь дней. Для `MySpecialTable`таблицы установите политику удержания на уровне таблицы с периодом мягкого удаления в 100 лет (политика удержания по умолчанию).

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```