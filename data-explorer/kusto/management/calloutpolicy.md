---
title: Политика вызова вызова - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описана политика вызова в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: df57c4901cef74d574108d2c6e75672d1faba75c
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744511"
---
# <a name="callout-policy"></a>Политика внешних вызовов

## <a name="overview"></a>Обзор

Кластеры Azure Data Explorer могут общаться с внешними службами в различных сценариях.
Кластерные админы могут управлять разрешенными доменами для внешних вызовов, обновляя политику вызова кластера.

Политики вызова управляются на уровне кластеров и классифицируются по следующим типам:
* `kusto`- управление запросами по межкластерным кластерам Azure Data Explorer.
* `sql`- контролирует [плагин S'L](../query/sqlrequestplugin.md).


* `webapi`- контролирует другие внешние веб-звонки.
* `sandbox_artifacts`- контролирует песочницы плагинов[(питон](../query/pythonplugin.md) | [R](../query/rplugin.md)).

Политика вызова состоит из следующих компонентов:
* **CalloutType** определяет тип вызова и может быть одним из следующих: kusto, sql или webapi
* **CalloutUriRegex** указывает разрешенный Regex домена callout
* **CanCall** указывает, позволяет ли вызов внешних вызовов.

## <a name="predefined-callout-policies"></a>Предопределенные политики Callout

Существует набор предопределенных политик вызова, непреложно настроенных на все кластеры Azure Data Explorer для облегчения вызовов для выбора служб.

|Служба      |Облако        |Категория  |Разрешенные домены |
|-------------|-------------|-------------|-------------|
|Kusto |Public Azure (Общедоступные Azure) |Перекрестные кластерные запросы |`^[^.]*\.kusto\.windows\.net$` <br> `^[^.]*\.kustomfa\.windows\.net$` |
|Kusto |Шварцвальд |Перекрестные кластерные запросы |`^[^.]*\.kusto\.cloudapi\.de$` <br> `^[^.]*\.kustomfa\.cloudapi\.de$` |
|Kusto |Fairfax |Перекрестные кластерные запросы |`^[^.]*\.kusto\.usgovcloudapi\.net$` <br> `^[^.]*\.kustomfa\.usgovcloudapi\.net$` |
|Kusto |Mooncake |Перекрестные кластерные запросы |`^[^.]*\.kusto\.chinacloudapi\.cn$` <br> `^[^.]*\.kustomfa\.chinacloudapi\.cn$` |
|Azure DB |Public Azure (Общедоступные Azure) |Запросы на S'L |`^[^.]*\.database\.windows\.net$` <br> `^[^.]*\.databasemfa\.windows\.net$` |
|Azure DB |Шварцвальд |Запросы на S'L |`^[^.]*\.database\.cloudapi\.de$` <br> `^[^.]*\.databasemfa\.cloudapi\.de$` |
|Azure DB |Fairfax |Запросы на S'L |`^[^.]*\.database\.usgovcloudapi\.net$` <br> `^[^.]*\.databasemfa\.usgovcloudapi\.net$` |
|Azure DB |Mooncake |Запросы на S'L |`^[^.]*\.database\.chinacloudapi\.cn$` <br> `^[^.]*\.databasemfa\.chinacloudapi\.cn$` |
|Базовый сервис |Public Azure (Общедоступные Azure) |Базовые запросы |`baseliningsvc-int.azurewebsites.net` <br> `baseliningsvc-ppe.azurewebsites.net` <br> `baseliningsvc-prod.azurewebsites.net` |


## <a name="control-commands"></a>Управляющие команды

Команды требуют разрешений [AllDatabasesAdmin.](access-control/role-based-authorization.md)

**Отображайте все настроенные политики вызова**

```kusto
.show cluster policy callout
```

**Изменение политики выхода вызывающего вызова**

```kusto
.alter cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}]'
```

**Добавление набора допустимых вызовов**

```kusto
.alter-merge cluster policy callout @'[{"CalloutType": "webapi","CalloutUriRegex": "en\\.wikipedia\\.org","CanCall": true}, {"CalloutType": "webapi","CalloutUriRegex": "bing\\.com","CanCall": true}]'
```

**Удалить все непреложные политики вызова**

```kusto
.delete cluster policy callout
```
