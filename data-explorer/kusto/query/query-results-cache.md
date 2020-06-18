---
title: Кэш результатов запроса — Azure обозреватель данных
description: В этой статье описываются функции кэша результатов запросов в Azure обозреватель данных.
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3c1e1eec2bb0ad4d856ca690039dea9aedd78e8f
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943130"
---
# <a name="query-results-cache"></a>Кэш результатов запроса

Kusto включает кэш результатов запроса. Можно указать готовность к получению кэшированных результатов при выдаче запроса. Если результаты запроса могут быть возвращены кэшем, вы получите лучшую производительность запросов и уменьшите потребление ресурсов за счет некоторых «устаревших» результатов.

## <a name="indicating-willingness-to-use-the-cache"></a>, Указывающее готовность к использованию кэша

Задайте `query_results_cache_max_age` параметр в составе запроса, чтобы указать готовность к использованию кэша результатов запроса. Этот параметр можно задать либо в тексте запроса, либо в виде свойства запроса клиента. Пример:

```kusto
set query_results_cache_max_age = time(5m);
GithubEvent
| where CreatedAt > ago(180d)
| summarize arg_max(CreatedAt, Type) by Id
```

Параметр имеет значение `timespan` , которое указывает максимальный возраст кэша результатов, измеряемый с момента начала запроса. Помимо набора TimeSpan, запись кэша устарела и не будет использоваться снова. Установка значения 0 эквивалентно отсутствию параметра.

## <a name="how-compatibility-between-queries-is-determined"></a>Как определяется совместимость между запросами

Query_results_cache возвращает результаты только для тех запросов, которые считаются идентичными предыдущим кэшированным запросом. Два запроса считаются идентичными, если выполняются все перечисленные ниже условия.

* Два запроса имеют одно и то же представление (в виде строк UTF-8).

* Два запроса выполняются в одной и той же базе данных.

* Два запроса совместно используют одни и те же [Свойства запросов клиента](../api/netfx/request-properties.md). Следующие свойства игнорируются в целях кэширования.
   * [ClientRequestId](../api/netfx/request-properties.md#the-clientrequestid-x-ms-client-request-id-named-property)
   * [Приложение](../api/netfx/request-properties.md#the-application-x-ms-app-named-property)
   * [Пользователь](../api/netfx/request-properties.md#the-user-x-ms-user-named-property)

## <a name="if-the-query-results-cache-cant-find-a-valid-cache-entry"></a>Если кэш результатов запроса не может найти допустимую запись кэша

Если не удалось найти кэшированный результат, который удовлетворяет ограничениям времени, или в кэше нет результатов, полученных из "идентичных" запросов, запрос будет выполнен и его результаты будут кэшироваться, если: 

* Выполнение запроса успешно завершено, и
* Размер результатов запроса не превышает 16 МБ.

## <a name="how-the-service-indicates-that-the-query-results-are-being-served-from-the-cache"></a>Как служба указывает, что результаты запроса обслуживаются из кэша

При ответе на запрос Kusto отправляет дополнительную таблицу ответов [расширенных свойств](../api/rest/response.md) , содержащую `Key` столбец и `Value` столбец.
В результаты кэшированного запроса будет добавлена дополнительная строка, добавленная к этой таблице:
* `Key`Столбец строки будет содержать строку`ServerCache`
* `Value`Столбец строки будет содержать контейнер свойств с двумя полями:
   * `OriginalClientRequestId`— Указывает [ClientRequestId](../api/netfx/request-properties.md#the-clientrequestid-x-ms-client-request-id-named-property)исходного запроса.
   * `OriginalStartedOn`— Указывает время начала выполнения исходного запроса.

## <a name="distribution"></a>Distribution

Кэш не является общим для узлов кластера. Каждый узел имеет выделенный кэш в своем собственном частном хранилище. Если два одинаковых запроса помещаются на разные узлы, запрос будет выполнен и кэширован на обоих узлах. Этот процесс может произойти, если используется [слабая согласованность](../concepts/queryconsistency.md) .

## <a name="management"></a>Управление

Поддерживаются следующие команды управления и наблюдения:

* [Отобразить кэш](../management/show-query-results-cache-command.md).
* [Очистить кэш](../management/clear-query-results-cache-command.md).

## <a name="capacity"></a>Capacity

Сейчас емкость кэша исфиксирована с 1 ГБ на узел кластера.
Политика вытеснения — LRU.
