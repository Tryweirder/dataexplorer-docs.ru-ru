---
title: Коды ошибок приема в Azure обозреватель данных
description: В этом разделе перечислены коды ошибок приема в Azure обозреватель данных
author: orspod
ms.author: orspodek
ms.reviewer: vladikbr
ms.service: data-explorer
ms.topic: reference
ms.date: 11/11/2020
ms.openlocfilehash: 18ac718edd50c804f71b9b82cbffb5b2b7bb2e24
ms.sourcegitcommit: 202357f866801aafd92e3e29a84bb312e17aebc7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96933781"
---
# <a name="ingestion-error-codes-in-azure-data-explorer"></a>Коды ошибок приема в Azure обозреватель данных

В следующем списке приведены коды ошибок, которые могут возникнуть во время [приема](ingest-data-overview.md). При включении в кластере [журналов диагностики](using-diagnostic-logs.md#ingestion-logs-schema) , которые не удалось запустить, можно просмотреть коды ошибок в журнале операций **приема** ошибок. Можно также отслеживать **результирующую** [метрику](using-metrics.md#ingestion-metrics) приема, чтобы просмотреть **категорию** ошибок приема, но не конкретные коды ошибок. Приведенные ниже ошибки упорядочены по этим категориям. 

> [!NOTE]
> Если ошибка является временной, повторная попытка приема может быть выполнена.

## <a name="category-badformat"></a>Категория: Бадформат

|Сообщение об ошибке                                 |Описание                                           |Постоянный/переходный|
|---|---|---|
|Stream_WrongNumberOfFields                        |Несоответствие количества полей во входных записях. HRESULT: 0x80DA0008      |Постоянно           |
|Stream_ClosingQuoteMissing                        |Недопустимый формат CSV. Отсутствует закрывающая кавычка. HRESULT: 0x80DA000b            |Постоянно           |
|BadRequest_InvalidBlob                            |Недопустимый BLOB-объект.                                                              |Постоянно           |
|BadRequest_EmptyArchive                           |Архив пуст.                                                             |Постоянно           |
|BadRequest_InvalidArchive                         |Недопустимый Архив.                                                           |Постоянно           |
|BadRequest_InvalidMapping                         |Не удалось проанализировать сопоставление приема.<br>Дополнительные сведения о создании сопоставления приема см. в разделе [сопоставления данных](./kusto/management/mappings.md).   |Постоянно           |
|BadRequest_InvalidMappingReference                |Недопустимая ссылка на сопоставление.            |Постоянно           |
|BadRequest_FormatNotSupported                     |Формат не поддерживается. Это может быть вызвано тем, что используется формат, не поддерживаемый определенным подключением к данным. <br>Дополнительные сведения о форматах данных, поддерживаемых обозреватель данных Azure для приема, см. в разделе [Поддерживаемые форматы данных](ingestion-supported-formats.md). |Постоянно          |
|BadRequest_InconsistentMapping                    |Поддерживаемое сопоставление приема не согласуется с существующей схемой таблицы. |Постоянно           |
|BadRequest_UnexpectedCharacterInInputStream       |Непредусмотренный символ во входном потоке.                                     |Постоянно           |

## <a name="category-badrequest"></a>Категория: BadRequest

|Сообщение об ошибке                                 |Описание                                           |Постоянный/переходный|
|---|---|---|
|BadRequest_EmptyBlob                              |BLOB-объект пуст.                                                               |Постоянно           |
|BadRequest_EmptyBlobUri                           |URI BLOB-объекта пуст.                                                           |Постоянно           |
|BadRequest_DuplicateMapping                       |Свойства приема включают как Инжестионмаппинг, так и Инжестионмаппингреференце, которые являются недопустимыми.              |Постоянно          |
|BadRequest_InvalidOrEmptyTableName                |Имя таблицы пустое или недопустимо.<br>Дополнительные сведения о соглашении об именовании Azure обозреватель данных см. в разделе [имена сущностей](./kusto/query/schema-entities/entity-names.md).    |Постоянно          |
|BadRequest_EmptyDatabaseName                      |Пустое имя базы данных.             |Постоянно          |
|BadRequest_EmptyMappingReference                  |В некоторых форматах должно быть принято сопоставление приема, а ссылка на сопоставление пуста.<br>Дополнительные сведения о сопоставлении см. в разделе [сопоставление данных](./kusto/management/mappings.md).        |Постоянно           |
|Download_BadRequest                               |Не удалось скачать источник из службы хранилища Azure из-за неправильного запроса.    |Постоянно           |
|BadRequest_MissingMappingtFailure                 |Форматы Avro и JSON должны быть приняты с параметром Инжестионмаппинг или Инжестионмаппингреференце.         |Постоянно           |
|Stream_NoDataToIngest                             |Не найдены данные для приема.<br>Для данных в формате JSON эта ошибка может означать, что формат JSON был недопустимым.        |Постоянно          |
|Stream_DynamicPropertyBagTooLarge                 |Данные содержат слишком большие значения в динамическом столбце. HRESULT: 0x80DA000E         |Постоянно          |
|General_BadRequest                                |Недопустимый запрос.            |Постоянно           |
|BadRequest_CorruptedMessage                       |Сообщение повреждено.    |Постоянно           |
|BadRequest_SyntaxError                            |Ошибка синтаксиса запроса.     |Постоянно           |
|BadRequest_ZeroRetentionPolicyWithNoUpdatePolicy  |Таблица имеет нулевую политику хранения и не является исходной таблицей для любой политики обновления.    |Постоянно           |
|BadRequest_CreationTimeEarlierThanSoftDeletePeriod|Время создания, указанное для приема, не входит в `SoftDeletePeriod` .<br>Дополнительные сведения о см `SoftDeletePeriod` . [в разделе объект политики](./kusto/management/retentionpolicy.md#the-policy-object).  |Постоянно   |
|BadRequest_NotSupported                           |Запрос не поддерживается.    |Постоянно           |
|Download_SourceNotFound                           |Не удалось скачать источник из службы хранилища Azure. Источник не найден.       |Постоянно       |
|BadRequest_EntityNameIsNotValid                   |Недопустимое имя сущности.<br>Дополнительные сведения о соглашении об именовании Azure обозреватель данных см. в разделе [имена сущностей](./kusto/query/schema-entities/entity-names.md).    |Постоянно           |
|BadRequest_MalformedIngestionProperty              |Свойство приема имеет неправильный формат.    |Постоянно           |
| BadRequest_IngestionPropertyNotSupportedInThisContext | Свойство приема не поддерживается в этом контексте| Постоянно

## <a name="category-dataaccessnotauthorized"></a>Категория: Датаакцесснотаусоризед

|Сообщение об ошибке                                 |Описание                                           |Постоянный/переходный|
|---|---|---|
|Download_AccessConditionNotSatisfied              |Не удалось скачать источник из службы хранилища Azure. Условие доступа не удовлетворено.     |Постоянно           |
|Download_Forbidden                                |Не удалось скачать источник из службы хранилища Azure. доступ запрещен".    |Постоянно           |
|Download_AccountNotFound                          |Не удалось скачать источник из службы хранилища Azure. Учетная запись не найдена.    |Постоянно           |
|BadRequest_TableAccessDenied                      |Отказано в доступе к таблице.<br>Дополнительные сведения см. [в разделе Авторизация на основе ролей в Kusto](./kusto/management/access-control/role-based-authorization.md).     |Постоянно           |
|BadRequest_DatabaseAccessDenied                   |Отказано в доступе к базе данных.<br>Дополнительные сведения см. [в разделе Авторизация на основе ролей в Kusto](./kusto/management/access-control/role-based-authorization.md).                        |Постоянно           |

## <a name="category-downloadfailed"></a>Категория: DownloadFailed

|Сообщение об ошибке                                 |Описание                                           |Постоянный/переходный|
|---|---|---|
|Download_NotTransient                             |Не удалось скачать источник из службы хранилища Azure. Произошла невременная ошибка                   |Постоянно           |
|Download_UnknownError                             |Не удалось скачать источник из службы хранилища Azure. Произошла неизвестная ошибка              |Временный           |


## <a name="category-entitynotfound"></a>Категория: Ентитинотфаунд

|Сообщение об ошибке                                 |Описание                                           |Постоянный/переходный|
|---|---|---|
|BadRequest_MappingReferenceWasNotFound            |Ссылка на сопоставление не найдена.   |Постоянно           |
|BadRequest_DatabaseNotExist                       |База данных не существует.          |Постоянно           |
|BadRequest_TableNotExist                          |Таблица не существует.          |Постоянно           |
|BadRequest_EntityNotFound                         |Сущность Azure обозреватель данных (например, сопоставление, база данных или таблица) не найдена.           |Постоянно           |

## <a name="category-filetoolarge"></a>Категория: Филетуларже

|Сообщение об ошибке                                 |Описание                                           |Постоянный/переходный|
|---|---|---|
|Stream_InputStreamTooLarge                        |Общий размер входных данных или одно поле в данных слишком велики. HRESULT: 0x80DA0009                 |Постоянно          |
|BadRequest_FileTooLarge                           |Размер большого двоичного объекта превысил максимально допустимый размер для приема.<br>Дополнительные сведения о предельном размере для приема см. в статье [Обзор приема данных в Azure обозреватель данных](ingest-data-overview.md#comparing-ingestion-methods-and-tools). |Постоянно           |

## <a name="category-internalserviceerror"></a>Категория: Интерналсервицееррор

|Сообщение об ошибке                                 |Описание                                           |Постоянный/переходный|
|---|---|---|
|General_InternalServerError                       |На сервере произошла внутренняя ошибка.                     |Временный          |
|General_TransientSchemaMismatch                   |Схема целевой таблицы при запуске приема не соответствует схеме при фиксации приема.         |Временный           |
|Время ожидания                                            |Операция прервана из-за превышения времени ожидания.     |Временный           |
|BadRequest_MessageExhausted                       |Не удалось принять данные, так как достигнуто максимальное число повторных попыток или максимальное время повтора.<br>Повторная попытка приема может быть выполнена.   |Временный          |

## <a name="category-updatepolicyfailure"></a>Категория: Упдатеполицифаилуре

|Сообщение об ошибке                                 |Описание                                           |Постоянный/переходный|
|---|---|---|
|UpdatePolicy_QuerySchemaDoesNotMatchTableSchema   |Не удалось вызвать политику обновления. Схема запроса не соответствует схеме таблицы.     |Постоянно           |
|UpdatePolicy_FailedDescendantTransaction          |Не удалось вызвать политику обновления. Не удалось изменить политику обновления потомков.    |Временный           |
|UpdatePolicy_IngestionError                       |Не удалось вызвать политику обновления. Произошла ошибка приема.<br>В исходной таблице политики обновления отображается сообщение об ошибке.     |Временный          |
|UpdatePolicy_UnknownError                         |Не удалось вызвать политику обновления. Произошла неизвестная ошибка.<br>В целевой таблице политики обновления отображается сообщение об ошибке.    |Временный           |
|UpdatePolicy_Cyclic_Update_Not_Allowed         |Не удалось вызвать политику обновления. Циклическое обновление запрещено.      |Постоянно           |

## <a name="category-useraccessnotauthorized"></a>Категория: Усеракцесснотаусоризед

|Сообщение об ошибке                                 |Описание                                           |Постоянный/переходный|
|---|---|---|
|BadRequest_InvalidKustoIdentityToken              |Недопустимый токен удостоверения Kusto.                           |Постоянно           |
|Запрещено                                          |Недостаточно разрешений безопасности для выполнения запроса. | Постоянно|

## <a name="category-unknown"></a>Категория: неизвестна

|Сообщение об ошибке                                 |Описание                                           |Постоянный/переходный|
|---|---|---|
|Неизвестно                                            |Произошла неизвестная ошибка.                             |Временный          |
