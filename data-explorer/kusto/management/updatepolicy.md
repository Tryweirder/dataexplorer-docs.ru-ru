---
title: Политика обновления Kusto для данных, добавляемых в исходный обозреватель данных Azure
description: В этой статье описывается политика обновления в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 072c908109fecb695a8961c546deb756caf830ab
ms.sourcegitcommit: 98eabf249b3f2cc7423dade0f386417fb8e36ce7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82868696"
---
# <a name="update-policy"></a>изменение политики;

Политика обновления задается в **целевой таблице** , которая указывает Kusto автоматически добавлять в нее данные при вставке новых данных в **исходную таблицу**. **Запрос** политики обновления выполняется для данных, вставленных в исходную таблицу. Это позволяет, например, создать одну таблицу как фильтрованное представление другой таблицы, возможно, с другой схемой, политикой хранения и т. д.

По умолчанию сбой запуска политики обновления не влияет на прием данных в исходную таблицу. Если политика обновления определена как **транзакционная**, сбой запуска политики обновления принудительно приводит к сбою приема данных в исходной таблице. (Осторожно следует использовать, когда это делается, поскольку некоторые ошибки пользователя, например определение неверного запроса в политике обновления, **могут препятствовать** приему данных в исходную таблицу.) Данные, полученные в "границе" политик обновления транзакций, становятся доступными для запроса в одной транзакции.

Запрос политики обновления выполняется в специальном режиме, в котором он видит только недавно полученные данные в исходную таблицу. Невозможно запросить уже полученные данные исходной таблицы как часть этого запроса. Это быстро приводит к приему в виде квадратичного времени.

Так как политика обновления определена в целевой таблице, передача данных в одну исходную таблицу может привести к выполнению нескольких запросов к этим данным. Порядок выполнения политик обновления не определен.

Например, предположим, что исходная таблица является высокоскоростной таблицей трассировки с интересными данными, отформатированными в виде свободного текстового столбца. Также предположим, что Целевая таблица (на которой определена политика обновления) принимает только определенные строки трассировки и с хорошо структурированной схемой, которая является преобразованием исходных текстовых данных с помощью [оператора Parse](../query/parseoperator.md)Kusto.

Политика обновления ведет себя аналогично обычному приему и подчиняется тем же ограничениям и рекомендациям. Например, она масштабируется с учетом размера кластера и работает более эффективно, если приемы выполняются в больших массах.

## <a name="commands-that-trigger-the-update-policy"></a>Команды, которые активируют политику обновления

Политики обновления вступают в силу, когда данные принимаются или перемещаются в (экстенты создаются в) в таблице с помощью любой из следующих команд:

* [. приема (Pull)](../management/data-ingestion/ingest-from-storage.md)
* [. прием (встроенный)](../management/data-ingestion/ingest-inline.md)
* [. Set |. append |. Set-или-Append |. Set-или-Replace](../management/data-ingestion/ingest-from-query.md)
* [. Перемещение экстентов](../management/extents-commands.md#move-extents)
* [. Замена экстентов](../management/extents-commands.md#replace-extents)

## <a name="the-update-policy-object"></a>Объект политики обновления

С таблицей может быть связано не менее одного или нескольких объектов политики обновления.
Каждый такой объект представлен в виде контейнера свойств JSON со следующими определенными свойствами:

|Свойство. |type |Описание  |
|---------|---------|----------------|
|IsEnabled                     |`bool`  |Указывает, включена ли политика обновления (true) или отключена (false)                                                                                                                               |
|Источник                        |`string`|Имя таблицы, которая активирует вызов политики обновления                                                                                                                                 |
|Запрос                         |`string`|Запрос Kusto CSL, который используется для получения данных для обновления.                                                                                                                           |
|IsTransactional               |`bool`  |Указывает, является ли политика обновления транзакционной или нет (значение по умолчанию — false). Сбой запуска политики обновления транзакций приводит к тому, что исходная таблица также не обновляется новыми данными.   |
|пропагатеинжестионпропертиес  |`bool`  |Указывает, что свойства приема (Теги экстентов и время создания), заданные во время приема в исходной таблице, также должны применяться к таблицам в производной таблице.                 |

## <a name="notes"></a>Примечания

* Запрос автоматически охватывает только недавно полученные записи.
* Запрос может вызывать хранимые функции.
* Каскадные обновления разрешены (`TableA` → `TableB` → `TableC` → >...)
* Когда политика обновления вызывается как часть `.set-or-replace` команды, поведение по умолчанию заключается в том, что данные в производных таблицах также заменяются, как и в исходной таблице.

## <a name="limitations"></a>Ограничения

* Исходная таблица и таблица, для которых определена политика обновления, **должны находиться в одной базе данных**.
* Запрос может **не** включать запросы между базами данных и перекрестными кластерами.
* В случае, если политики обновления определены в нескольких таблицах циклически, это обнаруживается во время выполнения, а цепочка обновлений вырезается (то есть данные отправляются только один раз в каждую таблицу в цепочке затронутых таблиц).
* При ссылке на `Source` таблицу в `Query` части политики (или в функциях, на которые ссылается последняя) убедитесь, что **не** используется полное имя таблицы (то `TableName` есть используйте, а **не** `database("DatabaseName").TableName` или `cluster("ClusterName").database("DatabaseName").TableName`).
* Запрос, который выполняется как часть политики обновления, **не** имеет доступа на чтение к таблицам, для которых включена [Политика рестриктедвиевакцесс](restrictedviewaccesspolicy.md) .
* Запрос политики обновления не может ссылаться на любую таблицу с включенной [политикой безопасность на уровне строк](./rowlevelsecuritypolicy.md) .
* `PropagateIngestionProperties`вступает в силу только при операциях приема. Если политика обновления запускается как часть команды `.move extents` или `.replace extents` , этот параметр **не** действует.

## <a name="retention-policy-on-the-source-table"></a>Политика хранения в исходной таблице

Таким образом, чтобы не хранить необработанные данные в исходной таблице, можно установить период обратимого удаления в [политике хранения](retentionpolicy.md)исходной таблицы, а также настроить политику обновления как транзакционную.

Результат будет следующим:
* Исходные данные не поддаются запросам из исходной таблицы.
* Исходные данные не сохраняются в долговременном хранилище в ходе операции приема.
* Повышение производительности операции.
* Сокращение ресурсов, использующих после приема, для фоновых операций очистки, выполняемых в [экстентах](../management/extents-overview.md) исходной таблицы.

## <a name="failures"></a>Сбои

В некоторых случаях прием данных в исходную таблицу завершается успешно, но во время приема в целевую таблицу произошел сбой политики обновления.

Ошибки, обнаруженные при обновлении политик, можно получить с помощью [команды. отобразить ошибки приема](../management/ingestionfailures.md), как показано ниже.
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

Ошибки рассматриваются следующим образом:

* **Нетранзакционная политика**: ошибка игнорируется Kusto. Ответственность за повторные попытки несет владелец данных.  
* **Политика транзакций**. Исходная операция приема, вызвавшая обновление, также завершится ошибкой. Исходная таблица и база данных не будут изменены новыми данными.
  * В случае с методом приема `pull` (Kusto служба управление данными участвует в процессе приема), существует автоматическая повторная попытка выполнения всей операции приема, управляемой управление данныминой службой в соответствии со следующей логикой:
    * Повторы выполняются, пока не достигнуто самое раннее `DataImporterMaximumRetryPeriod` между (по умолчанию — 2 `DataImporterMaximumRetryAttempts` дня) и (значение по умолчанию — 10).
    * Оба указанных выше параметра можно изменить в конфигурации службы Управление данными по Куступс.
    * Период отсрочки начинается с 2 минут и растет экспоненциально (2-> 4-> 8-> 16... тезис
  * В любом другом случае ответственность за повторные попытки несет владелец данных.



## <a name="control-commands"></a>Команды управления

* Используйте [. отобразить таблицу таблицы обновление политики](../management/update-policy.md#show-update-policy) для отображения текущей политики обновления таблицы.
* Чтобы задать текущую политику обновления таблицы, используйте [инструкцию. ALTER TABLE Update.](../management/update-policy.md#alter-update-policy)
* Для добавления к текущей политике обновления таблицы используйте [инструкцию. ALTER-Merge таблица с обновлением политики](../management/update-policy.md#alter-merge-table-table-policy-update) .
* Используйте [. удалить таблицу таблицы обновление политики](../management/update-policy.md#delete-table-table-policy-update) для добавления к текущей политике обновления таблицы.

## <a name="testing-an-update-policys-performance-impact"></a>Тестирование влияния политики обновления на производительность

Определение политики обновления может повлиять на производительность кластера Kusto, поскольку он влияет на любые приемы в исходной таблице. Настоятельно рекомендуется оптимизировать `Query` часть политики обновления, чтобы работать правильно.
Вы можете протестировать дополнительное влияние политики обновления на производительность операции приема, вызвав ее в конкретных и уже существующих экстентах перед созданием или изменением политики или функции, которую она использует в своей `Query` части.

В нем предполагается следующее:

* Имя исходной таблицы ( `Source` свойство политики обновления) имеет значение. `MySourceTable`
* `Query` Свойство политики обновления вызывает функцию с именем `MyFunction()`.

С помощью [. Показывать запросы](../management/queries.md)можно оценить использование ресурсов (ЦП, память и т. д.) следующего запроса и (или) несколько его выполнений.

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```