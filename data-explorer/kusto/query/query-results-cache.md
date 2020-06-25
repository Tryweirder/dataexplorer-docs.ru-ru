---
title: Кэш результатов запроса — Azure обозреватель данных
description: В этой статье описываются функции кэша результатов запросов в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 4bfc7b591683940e10d2737ec776421f65d1427a
ms.sourcegitcommit: 93510ef1e5570ce4da2cbf76eb77946c93a7dec8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85372474"
---
# <a name="query-results-cache"></a>Кэш результатов запроса

Kusto включает кэш результатов запроса. При выдаче запроса можно выбрать получение кэшированных результатов. Если результаты запроса могут быть возвращены кэшем, производительность запросов и более низкое потребление ресурсов будут работать быстрее. Тем не менее, эта производительность связана с расходом на некоторое «устаревание» в результатах.

## <a name="use-the-cache"></a>Использование кэша

Задайте `query_results_cache_max_age` параметр в составе запроса, чтобы использовать кэш результатов запроса. Этот параметр можно задать в тексте запроса или в свойстве запроса клиента. Пример:

```kusto
set query_results_cache_max_age = time(5m);
GithubEvent
| where CreatedAt > ago(180d)
| summarize arg_max(CreatedAt, Type) by Id
```

Параметр имеет значение `timespan` , которое указывает максимальный возраст кэша результатов, измеряемый с момента начала запроса. Помимо набора TimeSpan, запись кэша устарела и не будет использоваться снова. Установка значения 0 эквивалентно отсутствию параметра.

## <a name="compatibility-between-queries"></a>Совместимость запросов

### <a name="identical-queries"></a>Идентичные запросы

Кэш результатов запросов возвращает результаты только для запросов, которые считаются идентичными предыдущим кэшированным запросом. Два запроса считаются идентичными, если выполняются все перечисленные ниже условия.

* Два запроса имеют одно и то же представление (в виде строк UTF-8).
* Два запроса выполняются в одной и той же базе данных.
* Два запроса совместно используют одни и те же [Свойства запросов клиента](../api/netfx/request-properties.md). Следующие свойства игнорируются в целях кэширования.
   * [ClientRequestId](../api/netfx/request-properties.md#the-clientrequestid-x-ms-client-request-id-named-property)
   * [Приложение](../api/netfx/request-properties.md#the-application-x-ms-app-named-property)
   * [Пользователь](../api/netfx/request-properties.md#the-user-x-ms-user-named-property)

### <a name="incompatible-queries"></a>Несовместимые запросы

Результаты запроса не будут кэшироваться, если выполняется любое из следующих условий.
 
* Запрос ссылается на таблицу с включенной политикой [рестриктедвиевакцесс](../management/restrictedviewaccesspolicy.md) .
* Запрос ссылается на таблицу с включенной политикой [ровлевелсекурити](../management/rowlevelsecuritypolicy.md) .
* В запросе используются следующие функции.
    * [current_principal](current-principalfunction.md)
    * [current_principal_details](current-principal-detailsfunction.md)
    * [current_principal_is_member_of](current-principal-ismemberoffunction.md)
* Запрос является [запросом между кластерами](cross-cluster-or-database-queries.md).
* Запрос обращается к [внешней таблице](schema-entities/externaltables.md) или [внешним данным](externaldata-operator.md).
* В запросе используется оператор [Evaluate подключаемого модуля](evaluateoperator.md) .

## <a name="no-valid-cache-entry"></a>Нет допустимой записи кэша

Если не удалось найти кэшированный результат, который удовлетворяет ограничениям времени, или в кэше нет результатов, полученных из "идентичных" запросов, запрос будет выполнен и его результаты будут кэшироваться, если: 

* Выполнение запроса успешно завершено, и
* Размер результатов запроса не превышает 16 МБ.

## <a name="results-from-the-cache"></a>Результаты из кэша

Как служба указывает, что результаты запроса обслуживаются из кэша?
При ответе на запрос Kusto отправляет дополнительную таблицу ответов [расширенных свойств](../api/rest/response.md) , содержащую `Key` столбец и `Value` столбец.
В результаты кэшированного запроса будет добавлена дополнительная строка, добавленная к этой таблице:
* `Key`Столбец строки будет содержать строку`ServerCache`
* `Value`Столбец строки будет содержать контейнер свойств с двумя полями:
   * `OriginalClientRequestId`— Указывает [ClientRequestId](../api/netfx/request-properties.md#the-clientrequestid-x-ms-client-request-id-named-property)исходного запроса.
   * `OriginalStartedOn`— Указывает время начала выполнения исходного запроса.

## <a name="distribution"></a>Распределение

Кэш не является общим для узлов кластера. Каждый узел имеет выделенный кэш в своем собственном частном хранилище. Если два одинаковых запроса помещаются на разные узлы, запрос будет выполнен и кэширован на обоих узлах. Этот процесс может произойти, если используется [слабая согласованность](../concepts/queryconsistency.md) .

## <a name="management"></a>Управление

Поддерживаются следующие команды управления и наблюдения:

* [Отобразить кэш](../management/show-query-results-cache-command.md).
* [Очистить кэш](../management/clear-query-results-cache-command.md).

## <a name="capacity"></a>Capacity

Сейчас емкость кэша исфиксирована с 1 ГБ на узел кластера.
Политика вытеснения — LRU.
