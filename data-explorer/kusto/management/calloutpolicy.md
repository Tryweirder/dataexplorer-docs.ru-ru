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
ms.openlocfilehash: 42254e00e629a19dfceeef2d4a6c2d1877400c05
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011556"
---
# <a name="callout-policy"></a>Политика внешних вызовов

Кластеры Azure обозреватель данных могут взаимодействовать с внешними службами во многих разных сценариях.
Администраторы кластера могут управлять полномочными доменами для внешних вызовов путем обновления политики выноски кластера.

Политики выноски управляются на уровне кластера и классифицируются в следующие типы.
* `kusto`— Управляет обозреватель данных запросов между кластерами в Azure.
* `sql`— Управляет [подключаемым модулем SQL](../query/sqlrequestplugin.md).

* `webapi`— Управляет другими внешними веб-вызовами.
* `sandbox_artifacts`— Управляет изолированными подключаемыми модулями ([Python](../query/pythonplugin.md)  |  [R](../query/rplugin.md)).

Политика выноски состоит из следующих элементов.

* **Каллауттипе** — определяет тип выноски и может иметь значение `kusto` , `sql` или`webapi`
* **Каллаутурирежекс** — Указывает разрешенное регулярное выражение для домена выноски
* **Канкалл** — указывает, разрешены ли в выноске внешние вызовы.

## <a name="predefined-callout-policies"></a>Стандартные политики выноски

В таблице показан набор стандартных политик выноски, которые предварительно настроены во всех кластерах Azure обозреватель данных для включения выносок для выбора служб.

|Служба      |Cloud        |Категория  |Разрешенные домены |
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
.alter cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}]'
```

**Добавление набора разрешенных выносок**

```kusto
.alter-merge cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}, {"CalloutType": "webapi","CalloutUriRegex": "bing\\.com","CanCall": true}]'
```

**Удалить все неизменяемые политики выноски**

```kusto
.delete cluster policy callout
```
