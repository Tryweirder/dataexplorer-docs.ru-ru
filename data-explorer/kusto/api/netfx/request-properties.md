---
title: Свойства запроса, ClientRequestProperties - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны свойства запроса, ClientRequestProperties в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/23/2019
ms.openlocfilehash: 0aa496e6011fce98db5a304b9748f27f07590b4f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524333"
---
# <a name="request-properties-clientrequestproperties"></a>Запрос свойств, ClientRequestProperties

При совершении запроса от Kusto через .NET SDK, как правило, приводится следующие значения:

1. Строка соединения, указывающая конечную точку службы для подключения к параметрам аутентификации и аналогичной информации, связанной с подключением. Программно строка соединения представлена `KustoConnectionStringBuilder` через класс.

2. Название базы данных, используемой для описания "сферы" запроса.

3. Сам текст запроса (запрос или команда).

4. Дополнительные свойства, которые клиент предоставляет услугу и применяются к запросу. Программно, эти свойства проводятся класспод `ClientRequestProperties`названием .

Свойства запроса клиента имеют много применений. Некоторые из них используются для упрощения отладки (например, путем предоставления строк корреляции, которые могут быть использованы для отслеживания взаимодействия клиентов/служб), другие используются для влияния на то, какие ограничения и политики применяются к запросу, в то время как третья категория таких свойств — [параметры запроса.](../../query/queryparametersstatement.md) Полный список поддерживаемых свойств отображается на этой странице.

Класс `Kusto.Data.Common.ClientRequestProperties` содержит три типа данных:

* Именованные свойства.

* Параметры (отображение имени опциона к значению опциона).

* Параметры (отображение значения параметра запроса к значению параметра запроса).

> [!NOTE]
> Некоторые названные свойства помечены как "не использовать". Такие свойства не должны быть указаны клиентами и не оказывают влияния на сервис.

## <a name="the-clientrequestid-x-ms-client-request-id-named-property"></a>КлиентЗапросId (x-ms-client-request-id) назвал свойство

Это имениемое свойство содержит указанную клиентом идентификацию запроса. Настоятельно рекомендуется указать уникальное значение каждого запроса с каждым запросом, который они отправляют, так как это упрощает сбои отладки (и требуется в некоторых сценариях, таких как отмена запроса).

Программное название этого свойства, `ClientRequestId`и это переводится в `x-ms-client-request-id`заголовок HTTP .

Это свойство будет установлено sDK (случайным) значением, если клиент не укажет свое собственное значение.

Содержание этого свойства может быть любой печатной уникальной строки, например GUID.
Однако клиентам рекомендуется использовать следующий шаблон:

*ПриложениеНазовусть* `.` *активностиУникальный* `;` *UniqueId*

В тех случаях, когда *ApplicationName* идентифицирует клиентское приложение, совершая запрос, *ActivityName* определяет вид деятельности, на который клиентское приложение выдает клиентский запрос, и *UniqueId* определяет конкретный запрос.

## <a name="the-application-x-ms-app-named-property"></a>Приложение (x-ms-app) названо свойство

Это имя собственности содержит имя клиентского приложения, делающих запрос, для целей отслеживания.

Программное название этого свойства, `Application`и это переводится в `x-ms-app`заголовок HTTP . Это может быть указано в строке соединения Kusto как `Application Name for Tracing`.

Это свойство будет устанавливаться на название процесса размещения SDK, если клиент не укажет свою собственную стоимость.

## <a name="the-user-x-ms-user-named-property"></a>Пользователь (x-ms-user) назвал свойство

Это имя свойства содержит личность пользователя, делающего запрос, для целей отслеживания.

Программное название этого свойства, `User`и это переводится в `x-ms-user`заголовок HTTP . Это может быть указано в строке соединения Kusto как `User Name for Tracing`.

## <a name="controlling-request-properties-using-the-rest-api"></a>Управление свойствами запросов с помощью REST API

При выдаче запроса HTTP службе Kusto используйте `properties` слот в документе JSON, который является органом запроса POST, чтобы предоставить свойства запроса. Обратите внимание, что некоторые свойства (например, "идентификатор запроса клиента", который является идентификатором корреляции, который клиент предоставляет службе для идентификации запроса), могут быть предоставлены в заголовке HTTP, и поэтому также может быть установлен, если используется HTTP GET.
Дополнительную информацию можно узнать [на объекте запроса Kusto REST API.](../rest/request.md)

## <a name="providing-values-for-query-parametrization-as-request-properties"></a>Предоставление значений для параметризации запросов в качестве свойств запросов

Запросы Kusto могут ссылаться на параметры запроса, используя специализированную выписку о [параметрах запроса](../../query/queryparametersstatement.md) в тексте запроса. Это позволяет клиентским приложениям параметризировать запросы Kusto на основе пользовательского ввода безопасным способом (без страха инъекций атак).

Программно, можно установить значения свойств с `ClearParameter` `SetParameter`помощью, и `HasParameter` методы.

В API REST параметры запроса отосважились в той же строке, что и другие свойства запроса.

## <a name="sample-code-for-using-request-properties"></a>Пример кода для использования свойств запроса

Вот пример для кода клиента:

```csharp
public static System.Data.IDataReader QueryKusto(
    Kusto.Data.Common.ICslQueryProvider queryProvider,
    string databaseName,
    string query)
{
    var queryParameters = new Dictionary<String, String>()
    {
        { "xIntValue", "111" },
        { "xStrValue", "abc" },
        { "xDoubleValue", "11.1" }
    };

    // Query parameters (and many other properties) are provided
    // by a ClientRequestProperties object handed alongside
    // the query:
    var clientRequestProperties = new Kusto.Data.Common.ClientRequestProperties(
        principalIdentity: null,
        options: null,
        parameters: queryParameters);

    // Having client code provide its own ClientRequestId is
    // highly recommended. It not only allows the caller to
    // cancel the query, but also makes it possible for the Kusto
    // team to investigate query failures end-to-end:
    clientRequestProperties.ClientRequestId
        = "MyApp.MyActivity;"
        + Guid.NewGuid().ToString();

    // This is an example for setting an option
    // ("notruncation", in this case). In most cases this is not
    // needed, but it's included here for completeness:
    clientRequestProperties.SetOption(
        Kusto.Data.Common.ClientRequestProperties.OptionNoTruncation,
        true);
 
    try
    {
        return queryProvider.ExecuteQuery(query, clientRequestProperties);
    }
    catch (Exception ex)
    {
        Console.WriteLine(
            "Failed invoking query '{0}' against Kusto."
            + " To have the Kusto team investigate this failure,"
            + " please open a ticket @ https://aka.ms/kustosupport,"
            + " and provide: ClientRequestId={1}",
            query, clientRequestProperties.ClientRequestId);
        return null;
    }
}
```

## <a name="list-of-clientrequestproperties"></a>Список клиентовЗапросСвойства

<!-- The following is auto-generated by running the following command: -->
<!-- Kusto.Cli.exe -execute:"#crp -doc"                                -->

<!-- The following text can be re-produced by running the Kusto.Cli.exe directive '#crp -doc' -->

<!-- The following text can be re-produced by running the Kusto.Cli.exe directive '#crp -doc' -->

* `debug_query_externaldata_projection_fusion_disabled`(*OptionDebug-EryDisableExternalDataProjectionFusion*: Если установлен, не предохраняйте проекцию в оператора ExternalData. (Булеан) Я не сказал, что я не против.
* `debug_query_fanout_threads_percent_external_data`(*OptionDebug'eryFanoutThreadsПроцентВнешниеДанные*:Процент потоков для исполнения фанаута для внешних узлов данных. (В инт)
* `deferpartialqueryfailures`(*OptionDeferPartial-EryFailures*: Если это правда, отклоняет сообщения о частичном сбоях запроса как часть набора результатов. (Булеан) Я не сказал, что я не против.
* `max_memory_consumption_per_query_per_node`(*OptionMaxMemoryConsumptionPer'eryPerNode*): Переопределяет максимальное количество памяти, которое весь запрос может выделить на узлы. (UInt64)
* `maxmemoryconsumptionperiterator`(*OptionMaxMemoryConsumptionPerIterator*): Переопределяет максимальное количество памяти, которое может выделить оператор запроса. (UInt64)
* `maxoutputcolumns`(*OptionMaxOutputColumns*): Переопределяет максимальное количество столбцов по умолчанию, которые может быть предоставлен запрос. (Долго)
* `norequesttimeout`(*OptionNoRequestTimeout*): Позволяет установить тайм-аут запроса до максимального значения. (Булеан) Я не сказал, что я не против.
* `notruncation`(*OptionNoTruncation*): Включает в себя подавление усечения результатов запроса, возвращенного вызывающему. (Булеан) Я не сказал, что я не против.
* `push_selection_through_aggregation`(*OptionPushSelectionThroughAggregation*: Если это правда, нажмите простой выбор через агрегацию »Boolean»
* `query_admin_super_slacker_mode`(*OptionAdminSuperSlackerMode*: Если это правда, делегируйте выполнение запроса другому узлу (Boolean)
* `query_bin_auto_at`(*QueryBinAutoAt*): При оценке функции bin_auto() начальное значение для использования. (Литературноевыражение)
* `query_bin_auto_size`(*QueryBinAutoSize*): При оценке функции bin_auto() значение размера ядра для использования. (Литературноевыражение)
* `query_cursor_after_default`(*Option'cursorAfterDefault*: Значение параметра по умолчанию cursor_after() функции при вызове без параметров. [string]
* `query_cursor_before_or_at_default`(*Option'cursorBeforeOrAtDefault*): Значение параметра по умолчанию cursor_before_or_at() функции при вызове без параметров. [string]
* `query_cursor_current`(*Option'eryCursorCurrent*): Переопределяет значение курсора, возвращенное cursor_current () или current_cursor () функций. [string]
* `query_cursor_scoped_tables`(*Option'eryCursorScopedTables*: Список имен таблиц, которые должны быть cursor_after_default.. cursor_before_or_at_default (верхняя граница не обязательна). (динамический)
* `query_datascope`(*Option'eryDataScope*: Контролирует данный скоп запроса - относится ли запрос ко всем данным или только к его части. «По умолчанию», «все» или «горячий кэш»
* `query_datetimescope_column`(*Option'eryDateTimeScopeColumn*): Контролирует название столбца для области дата запроса (query_datetimescope_to/query_datetimescope_from). (Струна)
* `query_datetimescope_from`(*Option'eryDateTimeScopeFrom*: Контролирует область времени действия запроса (самая ранняя) - используется в качестве автоматического прикладного фильтра только на query_datetimescope_column (если определено). [DateTime]
* `query_datetimescope_to`(*Option'eryDateTimeScopeTo*: Контролирует область времени действия запроса (последняя) - используется в качестве автоматического прикладного фильтра только на query_datetimescope_column (если она определена). [DateTime]
* `query_distribution_nodes_span`(*Option'eryDistributionNodesSpanSize*: Если набор, контролирует способ слияния подзапросов: исполнительный узло введет дополнительный уровень в иерархии запросов для каждой подгруппы узлов; размер подгруппы устанавливается этим вариантом. (В инт)
* `query_fanout_nodes_percent`(*Option'e'efanoutNodesПроцент*: Процент узлов для исполнения фанаута. (В инт)
* `query_fanout_threads_percent`(*Option'e'ryFanoutThreadsPercent*: Процент потоков для исполнения фанаута. (В инт)
* `query_language`(*Option'eryLanguage*: Контролирует, как должен интерпретироваться текст запроса. «csl»,,'kql' или 'sql'»
* `query_max_entities_in_union`(*OptionMaxEntitiesToUnion*): Переопределяет максимальное количество столбцов по умолчанию, которые может быть предоставлен запрос. (Долго)
* `query_now`(*Option'eryNow*): Переопределяет значение времени даты, возвращенное функцией now (0s). [DateTime]
* `query_python_debug`(*OptionDebugPython*: Если установлен, сгенерировать запрос отладки питона для перечисленных узла питона (по умолчанию в первую очередь). (Булеан или Инт)
* `query_results_apply_getschema`(*Option'eryResultsApplySchemchema*: Если установить, извлекает схему каждого табликовых данных в результатах запроса, а не сами холостого числа данных. (Булеан) Я не сказал, что я не против.
* `query_results_cache_max_age`(*Option'eryResultsCacheMaxAge*: Если положительный, контролирует максимальный возраст кэшированных результатов запроса, которые Кусто может вернуть «TimeSpan»
* `query_results_progressive_row_count`(*OptionProgressive'eryMinRowCountPerUpdate*): Подсказка для Кусто о том, сколько записей для отправки в каждом обновлении (вступает в силу только в том случае, если OptionResultsProgressiveEnabled установлен)
* `query_results_progressive_update_period`(*OptionProgressiveProgressReportPeriod*: Подсказка для Кусто о том, как часто отправлять кадры прогресса (вступает в силу только в том случае, если OptionResultsProgressiveEnabled установлен)
* `query_shuffle_broadcast_join`(*ShuffleBroadcastJoin*): Включает перетасовку по трансляции присоединиться.
* `query_take_max_records`(*OptionTakeMaxRecords*: Позволяет ограничить результаты запроса этим числом записей. (Долго)
* `queryconsistency`(*Опционная консистенция*: Управление согласованностью запроса. «сильная последовательность» или «нормальная последовательность» или «слабая последовательность»
* `request_callout_disabled`(*OptionRequestCalloutDisabled*: Если указано, указывается, что запрос не может вызвать в службу, предоставленную пользователем. (Булеан) Я не сказал, что я не против.
* `request_description`(*OptionRequestDescription*: Произвольный текст, который автор запроса хочет включить в качестве описания запроса. (Струна)
* `request_external_table_disabled`(*OptionRequestExternalTableDisabled*: Если указано, указывается, что запрос не может ссылаться на код в ExternalTable. (Булеан) Я не сказал, что я не против.
* `request_readonly`(*OptionRequestReadOnly*: Если указано, указывает, что запрос не должен быть в состоянии написать что-нибудь. (Булеан) Я не сказал, что я не против.
* `request_remote_entities_disabled`(*OptionRequestRemoteEntitiesDisabled*: Если указано, указывается, что запрос не может получить доступ к удаленным базам данных и кластеров. (Булеан) Я не сказал, что я не против.
* `request_sandboxed_execution_disabled`(*OptionRequestSandboxedExecutionDisabled*): Если указано, указывает, что запрос не может ссылаться на код в песочнице. (Булеан) Я не сказал, что я не против.
* `results_progressive_enabled`(*OptionResultsProgressiveEnabled*: Если набор, позволяет прогрессивный поток запросов
* `servertimeout`(*OptionServerTimeout*): Перекрывает тайм-аут запроса по умолчанию. (TimeSpan)
* `truncationmaxrecords`(*OptionTruncationMaxRecords*): Переопределяет максимальное количество записей по умолчанию, который запрос может вернуть сяруку (усечение). (Долго)
* `truncationmaxsize`(*OptionTruncationMaxSize*): Переопределяет максимальный размер данных dfefault, который запрос может вернуть сяручут к вызывающему (усечению). (Долго)
* `validate_permissions`(*OptionValidatePermissions*): Проверяет разрешения пользователя на выполнение запроса и не выполняет сам запрос. (Булеан) Я не сказал, что я не против.

