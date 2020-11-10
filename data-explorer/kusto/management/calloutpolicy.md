---
title: Политика выноски — обозреватель данных Azure
description: В этой статье описывается политика вызова в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: da1cca764563b4ad2ce96ceaeb117e33d059303d
ms.sourcegitcommit: 25c0440cb0390b9629b819611844f1375de00a66
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94417614"
---
# <a name="callout-policy"></a>Политика внешних вызовов

Кластеры Azure обозреватель данных могут взаимодействовать с внешними службами во многих разных сценариях.
Администраторы кластера могут управлять полномочными доменами для внешних вызовов путем обновления политики выноски кластера.

Политики выноски управляются на уровне кластера и классифицируются в следующие типы.
* `kusto` — Управляет обозреватель данных запросов между кластерами в Azure.
* `sql` — Управляет [подключаемым](../query/sqlrequestplugin.md) модулем SQL и [подключаемым модулем mysql_request](../query/mysqlrequest-plugin.md).
* `cosmosdb` — Управляет [подключаемым модулем CosmosDB](../query/cosmosdb-plugin.md).
* `sandbox_artifacts`— Управляет изолированными подключаемыми модулями ([Python](../query/pythonplugin.md)  |  [R](../query/rplugin.md)).
* `external_data` — Управляет доступом к внешним данным с помощью [внешних таблиц](../query/schema-entities/externaltables.md) или оператора [externaldata](../query/externaldata-operator.md) .

Политика выноски состоит из следующих элементов.

* **Каллауттипе** — определяет тип вызова и может иметь значение `kusto` или `sql` .
* **Каллаутурирежекс** — Указывает разрешенное регулярное выражение для домена выноски
* **Канкалл** — указывает, разрешены ли в выноске внешние вызовы.

## <a name="predefined-callout-policies"></a>Стандартные политики выноски

В таблице показан набор стандартных политик выноски, которые предварительно настроены во всех кластерах Azure обозреватель данных для включения выносок для выбора служб.

|Служба      |Cloud        |Должность  |Разрешенные домены |
|-------------|-------------|-------------|-------------|
|Kusto |`Public Azure` |Запросы между кластерами |`^[^.]*\.kusto\.windows\.net$` <br> `^[^.]*\.kustomfa\.windows\.net$` |
|Kusto |`Black Forest` |Запросы между кластерами |`^[^.]*\.kusto\.cloudapi\.de$` <br> `^[^.]*\.kustomfa\.cloudapi\.de$` |
|Kusto |`Fairfax` |Запросы между кластерами |`^[^.]*\.kusto\.usgovcloudapi\.net$` <br> `^[^.]*\.kustomfa\.usgovcloudapi\.net$` |
|Kusto |`Mooncake` |Запросы между кластерами |`^[^.]*\.kusto\.chinacloudapi\.cn$` <br> `^[^.]*\.kustomfa\.chinacloudapi\.cn$` |
|База данных Azure |`Public Azure` |Запросы SQL |`^[^.]*\.database\.windows\.net$` <br> `^[^.]*\.databasemfa\.windows\.net$` |
|База данных Azure |`Black Forest` |Запросы SQL |`^[^.]*\.database\.cloudapi\.de$` <br> `^[^.]*\.databasemfa\.cloudapi\.de$` |
|База данных Azure |`Fairfax` |Запросы SQL |`^[^.]*\.database\.usgovcloudapi\.net$` <br> `^[^.]*\.databasemfa\.usgovcloudapi\.net$` |
|База данных Azure |`Mooncake` |Запросы SQL |`^[^.]*\.database\.chinacloudapi\.cn$` <br> `^[^.]*\.databasemfa\.chinacloudapi\.cn$` |
|Служба задания базовых показателей |Public Azure (Общедоступные Azure) |Запросы задания базовых показателей |`baseliningsvc-int.azurewebsites.net` <br> `baseliningsvc-ppe.azurewebsites.net` <br> `baseliningsvc-prod.azurewebsites.net` |

## <a name="control-commands"></a>Управляющие команды

Для команд требуются разрешения [аллдатабасесадмин](access-control/role-based-authorization.md) .

**Показывать все настроенные политики выноски**

```kusto
.show cluster policy callout
```

**Изменение политик выноски**

```kusto
.alter cluster policy callout @'[{"CalloutType": "sql","CalloutUriRegex": "sqlname.database.azure.com","CanCall": true}]'
```

**Добавление набора разрешенных выносок**

```kusto
.alter-merge cluster policy callout @'[{"CalloutType": "sql","CalloutUriRegex": "sqlname.database.azure.com","CanCall": true}]'
```

**Удалить все неизменяемые политики выноски**

```kusto
.delete cluster policy callout
```
