---
title: Справка Kusto.Ingest - Ошибки и исключения - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается ссылка Kusto.Ingest - Ошибки и исключения в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: f8f50322a79dea8890b4a4ad5eaa78f0b8fe3bc0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524350"
---
# <a name="kustoingest-reference---errors-and-exceptions"></a>Справка Kusto.Ingest - Ошибки и исключения
Любая ошибка при обработке при попадании на стороне клиента подвергается воздействию кода пользователя с помощью исключения c.'.

## <a name="failures-overview"></a>Обзор сбоев

### <a name="kustodirectingestclient-exceptions"></a>КустоНаправлениеКлиентов Исключения
При попытке глотнуть из нескольких источников, ошибки могут возникнуть во время приема некоторых из этих источников, в то время как другие могут быть поглощены успешно. Если проглатывание не удается для конкретного источника, он регистрируется и клиент продолжает глотать оставшиеся источники для приема. После перехода на все источники `IngestClientAggregateException` для приема, является `IList<IngestClientException> IngestionErrors`брошенным, содержащий член .
`IngestClientException`и его выведенные классы содержат поле `IngestionSource` и `Error` поле, которые вместе образуют отображение из источника, которое не было проглочено на ошибку, которая произошла при попытке глотать его. Можно использовать информацию в списке IngestionErrors, чтобы исследовать, какие источники не были проглатывается и почему. `IngestClientAggregateException`исключение также содержит свойство `GlobalError`boolean, которое указывает, произошла ли ошибка для всех источников.

### <a name="failures-ingesting-from-files-or-blobs"></a>Сбои, проглоки из файлов или blobs 
Если произошел сбой при попытке глотания из капли файла, источники приема не `deleteSourceOnSuccess` удаляются, даже если флаг установлен на `true`.
Источники сохранены для дальнейшего анализа. Понимая происхождение ошибки и учитывая, что ошибка не возникла из самого источника приема, пользователь клиента может попытаться повторно проглатывает ее.

### <a name="failures-ingesting-from-idatareader"></a>Сбои, проглокшие от IDataReader
При глотании от DataReader данные для проглотания сохраняются `<Temp Path>\Ingestions_<current date and time>`во временную папку, местоположение которой по умолчанию находится в состоянии . Эта папка всегда удаляется после успешного приема.<BR>
В `IngestFromDataReader` `IngestFromDataReaderAsync` методах `retainCsvOnFailure` и методах флаг, `false`значение по умолчанию которого является значением, определяет, следует ли хранить файлы после неудачного приема. Если этот флаг `false`установлен на , данные, которые не удается приема не будет сохраняться, что делает его трудно понять, что пошло не так.

## <a name="kustoqueuedingestclient-exceptions"></a>КустоОчередьдингесКлиент Исключения
Kusto'ueingIngClient глотает данные, загружая сообщения в очередь Azure. Если какая-либо ошибка возникает до `IngestClientAggregateException` и во время процесса очереди, `IngestClientException` в конце выполнения внего- собрание содержится источник, который не был размещен в очереди (за каждый сбой) и ошибка, возникшая при попытке размещения сообщения.

### <a name="posting-to-queue-failures-with-file-or-blob-as-a-source"></a>Публикация в сбоях в очереди с файлом или Blob в качестве источника
Если ошибка произошла при использовании методов IngestFromFile/IngestFromBlob у Kusto'ueingingclient, `deleteSourceOnSuccess` источники не `true`удаляются, даже если флаг установлен, а скорее сохранены для дальнейшего анализа. Понимая происхождение ошибки и учитывая, что ошибка не возникла из самого источника, пользователь клиента может попытаться повторно выставить данные, используя соответствующие методы IngestFromFile/IngestFromBlob с неисправным источником. 

### <a name="posting-to-queue-failures-with-idatareader-as-a-source"></a>Публикация в очереди сбоев с IDataReader в качестве источника
При использовании источника DataReader данные для публикации в очереди сохраняются `<Temp Path>\Ingestions_<current date and time>`во временную папку, местоположение которой по умолчанию находится в состоянии безопасности.
Эта папка всегда удаляется после успешного размещения данных в очереди.
В `IngestFromDataReader` `IngestFromDataReaderAsync` методах `retainCsvOnFailure` и методах флаг, `false`значение по умолчанию которого является значением, определяет, следует ли хранить файлы после неудачного приема. Если этот флаг `false`установлен на , данные, которые не удается приема не будет сохраняться, что делает его трудно понять, что пошло не так.

### <a name="common-failures"></a>Общие сбои
|Ошибка|Причина|Меры по снижению риска|
|------------------------------|----|------------|
|Имя <database name> базы данных не существует| База данных не существует|Проверьте имя базы данных на kustoIngestionProperties/Создать базу данных |
|Сущность 'таблица имя, которое не существует' рода 'Таблица' не было найдено.|Таблица не существует, и нет отображения CSV.| Добавить отображение CSV / создать необходимую таблицу |
|Blob <blob path> исключены по причине: Json шаблон должен быть попадает с jsonMapping параметр| Json приема, когда не Json отображение при условии.|Обеспечить отображение JSON |
|Не удалось скачать blob: "Удаленный сервер вернулся ошибка: (404) не найдено.| Капля не существует.|Проверить blob существует, если существует повторная попытка и связаться с командой Kusto |
|Отображение столбца Json недопустимо: два или более элементов отображения указывают на одну и ту же колонку.| JSON отображение имеет 2 столбца с различными путями|Исправить JSON отображение |
|EngineError - «UtilsException» IngestionDownloader.Download: Один или несколько файлов не удалось загрузить<GUID1>(поиск KustoLogs для ActivityID: , RootActivityId:<GUID2>)| Один или несколько файлов не удалось загрузить. |Повторить попытку |
|Не удалось разобрать: Поток с<stream name>id ' имеет пороки csv формат, не в соответствии с политикой ValidationOptions |Несформированный файл csv (например, не одинаковое количество столбцов на каждой строке). Не удается только при установке политики проверки на параметры валидации. ValidateCsvInputConstantColumns |Проверьте ваши файлы csv. Это сообщение применяется только в файлах csv/tsv |
|IngestClientAggregateException с сообщением об ошибке 'Missing обязательные параметры для действительной подписи общего доступа' |Используемый SAS — это услуга, а не учетная запись хранилища |Использование SAS учетной записи хранилища |

### <a name="ingestion-error-codes"></a>Коды ошибки при вшивке

Чтобы помочь в обработке сбоев в проглатывания хэтча, информация о сбое обогащается кодом ошибки (перечисление IngestionErrorCode).

|ErrorCode|Причина|
|-----------|-------|
|Неизвестно| Произошла неизвестная ошибка|
|Stream_LowMemoryCondition| Операция иссякла в памяти|
|Stream_WrongNumberOfFields| Документ CSV имеет несогласованное количество полей|
|Stream_InputStreamTooLarge| Документ, представленный для приема в приеме, превысил допустимый размер|
|Stream_NoDataToIngest| Найдено никаких потоков данных для проема|
|Stream_DynamicPropertyBagTooLarge| Один из динамических столбцов в просаченных данных содержит слишком много уникальных свойств|
|Download_SourceNotFound| Не удалось загрузить источник из хранилища Azure - источник не найден|
|Download_AccessConditionNotSatisfied| Не удалось загрузить источник из хранилища Azure - доступ отказано|
|Download_Forbidden| Не удалось скачать источник из хранилища Azure - доступ запрещен|
|Download_AccountNotFound| Не удалось загрузить источник из хранилища Azure - учетная запись не найдена|
|Download_BadRequest| Не удалось загрузить источник из хранилища Azure - плохой запрос|
|Download_NotTransient| Не удалось загрузить источник из хранилища Azure - не преходящая ошибка|
|Download_UnknownError| Не удалось скачать источник из хранилища Azure - неизвестная ошибка|
|UpdatePolicy_QuerySchemaDoesNotMatchTableSchema| Не удалось вызвать политику обновления. Схема запроса не соответствует схеме таблицы|
|UpdatePolicy_FailedDescendantTransaction| Не удалось вызвать политику обновления. Политика обновления транзакций не удалось потомку|
|UpdatePolicy_IngestionError| Не удалось вызвать политику обновления. Произошла ошибка приема|
|UpdatePolicy_UnknownError| Не удалось вызвать политику обновления. Произошла неизвестная ошибка|
|BadRequest_MissingJsonMappingtFailure| Json шаблон не попадает с jsonMapping параметр|
|BadRequest_InvalidOrEmptyBlob| Blob является недействительным или пустой почтовый архив|
|BadRequest_DatabaseNotExist| База данных не существует|
|BadRequest_TableNotExist| Таблица не существует|
|BadRequest_InvalidKustoIdentityToken| Недействительный токен идентификации kusto|
|BadRequest_UriMissingSas| Blob путь без SAS от неизвестного хранения капли|
|BadRequest_FileTooLarge| Попытка глотовать слишком большой файл|
|BadRequest_NoValidResponseFromEngine| Нет действительного ответа от команды ingest|
|BadRequest_TableAccessDenied| Отказ в доступе к таблице|
|BadRequest_MessageExhausted| Сообщение исчерпано|
|General_BadRequest| Общий плохой запрос (может намек на проглатывание на несуществующую базу данных/таблицу)|
|General_InternalServerError| Произошла внутренняя ошибка сервера|

## <a name="detailed-kustoingest-exceptions-reference"></a>Подробная ссылка на исключения Kusto.Ingest

### <a name="cloudqueuesnotfoundexception"></a>Облачные ОчередиНеНайдено Исключение
Поднятые, когда из кластера управления данными не возвращаются очереди

Базовый класс: [Исключение](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

Поля

|Имя|Тип|Значение
|-----------|----|------------------------------|
|Ошибка| `String`| Ошибка, возникшая при попытке извлечения очередей из DM
                            
Дополнительные сведения:

Соответствующие только при использовании [Kusto Очередь Ingest клиента](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient).
В процессе приема несколько попыток изымать очереди Azure, связанные с DM. При сбое этих попыток возникает исключение, содержащее причину сбоя в поле "Ошибка" и, возможно, внутреннее исключение в поле 'InnerException'.


### <a name="cloudblobcontainersnotfoundexception"></a>CloudBlobContainersNotFoundException
Поднятые, когда контейнеры с каплями не были возвращены из кластера управления данными

Базовый класс: [Исключение](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

Поля

|Имя|Тип|Значение       
|-----------|----|------------------------------|
|КустоЭндпойнт| `String`| Конечная точка соответствующего DM
                            
Дополнительные сведения:

Соответствующие только при использовании [Kusto Очередь Ingest клиента](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient).  
При глотании источников, которые уже не находятся в контейнере Azure, т.е. файлах, DataReader или Stream, данные загружаются во временную каплю для приема. Исключение возникает, когда нет контейнеров, найденных для загрузки данных.

### <a name="duplicateingestionpropertyexception"></a>ДубликационированиеPropertyИсключение
Поднято ежеразие при настройке свойства приема

Базовый класс: [Исключение](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

Поля

|Имя|Тип|Значение       
|-----------|----|------------------------------|
|PropertyName| `String`| Название дубликата свойства
                            
### <a name="postmessagetoqueuefailedexception"></a>PostmessageToQueueНеудалосьИсключение
Поднятое при отправке сообщения в очередь не удалось

Базовый класс: [Исключение](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

Поля

|Имя|Тип|Значение       
|-----------|----|------------------------------|
|Конюри| `String`| URI очереди
|Ошибка| `String`| Сообщение об ошибке, которое было сгенерировано при попытке публикации в очередь
                            
Дополнительные сведения:

Соответствующие только при использовании [Kusto Очередь Ingest клиента](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient).  
Клиент, выстроившееся в очередь, глотает данные, загружая сообщение в соответствующую очередь Azure. В случае сбоя публикации, выражается исключение, содержащее очередь URI, причина сбоя в поле "Ошибка" и, возможно, внутреннее исключение в поле 'InnerException'.

### <a name="dataformatnotspecifiedexception"></a>DataFormatNotSpecifiedИсключение
Поднят, когда требуется формат данных, но не указан в IngestionProperties

Базовый класс: IngestClientException

Дополнительные сведения:

При глотании из stream, формат данных должен быть указан в [IngestionProperties](kusto-ingest-client-reference.md#class-kustoingestionproperties) для того, чтобы правильно глотать данные. Это исключение возникает, когда IngestionProperties.Format не указан.

### <a name="invaliduriingestclientexception"></a>Недействительное исключение клиента
Поднятый, когда недействительный blob URI был представлен в качестве источника приема

Базовый класс: IngestClientException

### <a name="compressfileingestclientexception"></a>КомпрессияСамоеисключение клиента
Поднятый, когда клиент, проглокающий, не сдал файл, предусмотренный для приема

Базовый класс: IngestClientException

Дополнительные сведения:

Файлы сжимаются до их приема. Это исключение возникает при сбой попытки сжатия файла.

### <a name="uploadfiletotempblobingestclientexception"></a>UploadFileToTempBlobingestКлиентЫИсключение
Поднятый, когда клиент, проглативающийся, не смог загрузить источник, предусмотренный для приема на временную каплю

Базовый класс: IngestClientException

### <a name="sizelimitexceededingestclientexception"></a>SizelimitexceededingклиентыИсключения
Поднятый, когда источник приема слишком велик

Базовый класс: IngestClientException

Поля

|Имя|Тип|Значение       
|-----------|----|------------------------------|
|Размер| `long`| Размер источника приема
|MaxSize| `long`| Максимальный размер, допустимый при проглатываниях

Дополнительные сведения:

Если источник проглатывания превышает максимальный размер 4 ГБ, исключение выбрасывается. Проверка размера может быть переопределена флагом IgnoreSizeLimit в [классе IngestionProperties,](kusto-ingest-client-reference.md#class-kustoingestionproperties)однако не [рекомендуется глотать отдельные источники размером более 1 ГБ.](about-kusto-ingest.md#ingestion-best-practices)

### <a name="uploadfiletotempblobingestclientexception"></a>UploadFileToTempBlobingestКлиентЫИсключение
Поднятый, когда клиент, проглативающийся, не смог загрузить файл, предусмотренный для приема на временную каплю

Базовый класс: IngestClientException

### <a name="directingestclientexception"></a>РежиссерскаякомпанияКлиентИсключение
Поднят, когда общая ошибка произошла при выполнении прямого приема

Базовый класс: IngestClientException

### <a name="queuedingestclientexception"></a>ОчередьIngestКлиентЫ Исключение
Поднят при возникновении ошибки при выполнении проглатывания в очереди

Базовый класс: IngestClientException

### <a name="ingestclientaggregateexception"></a>ИнгестКлиентАГрегаксис
Поднято, когда одна или несколько ошибок произошли во время приема

Базовый класс: [AggregateException](https://msdn.microsoft.com/library/system.aggregateexception(v=vs.110).aspx)

Поля

|Имя|Тип|Значение       
|-----------|----|------------------------------|
|ОшибкаОшибки| `IList<IngestClientException>`| Ошибки, которые произошли при попытке глотать и источники, связанные с ними
|IsGlobalОшибка| `bool`| Указывает, произошло ли исключение для всех источников

## <a name="errors-in-native-code"></a>Ошибки в собственном коде
Двигатель Kusto написан в родном коде. Для получения подробной информации об ошибках в родном коде, пожалуйста, [см.](../../concepts/errorsinnativecode.md)