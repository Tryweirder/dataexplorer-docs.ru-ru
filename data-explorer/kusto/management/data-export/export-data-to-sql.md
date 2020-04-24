---
title: Экспортные данные в S'L - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны данные об экспорте в S'L в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7deeb3868800f00a828eb09cc605e4d7e5227032
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521630"
---
# <a name="export-data-to-sql"></a>Данные об экспорте в СЗЛ

Экспортные данные в S'L позволяют выполнять запрос и отправлять его результаты в таблицу в базе данных S'L, например, в базу данных S'L, размещенную службой базы данных Azure S'L.

**Синтаксис**

`.export`-`async` `to` `with` `(``,` `=` *PropertyValue* *PropertyName* *SqlTableName* SqlTableName *SqlConnectionString* - PropertyName PropertyValue ... `sql` `)`Запрос `<|` *Query*

Где:
* *async*: Команда выполняется в асинхронном режиме (по желанию).
* *SqlTableName* Имя таблицы базы данных S'L, в которой вставляются данные.
  Для защиты от инъекционных атак это имя ограничено.
* *SqlConnectionString* является `string` буквально, что `ADO.NET` следует формат строки соединения и описывает конечную точку и базу данных, к которой вы подключаетесь. По соображениям безопасности строка соединения ограничена.
* *PropertyName,* *PropertyValue* являются парами имени (идентификатор) и значением (строка буквально).

Свойства:

|Имя               |Значения           |Описание|
|-------------------|-----------------|-----------|
|`firetriggers`     |`true` или `false`|Если `true`, поручает целевой системе для запуска триггеров INSERT, определенных на таблице S'L. Значение по умолчанию — `false`. (Для получения дополнительной информации [см. BULK INSERT](https://msdn.microsoft.com/library/ms188365.aspx) и [System.Data.SqlClient.SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx))|
|`createifnotexists`|`true` или `false`|Если `true`целевая таблица S'L будет создана, если она еще не существует; свойство `primarykey` должно быть предоставлено в этом случае, чтобы указать столбец результата, который является основным ключом. Значение по умолчанию — `false`.|
|`primarykey`       |                 |Если `createifnotexists` `true`есть, указывается имя столбца в результате, которое будет использоваться в качестве основного ключа таблицы S'L, если оно создано этой командой.|
|`persistDetails`   |`bool`           |Указано, что команда должна `async` сохранять свои результаты (см. флаг). По умолчанию `true` в async выполняется, но может быть выключен, если абонент не требует результатов). По умолчанию в `false` синхронных исполнениях, но может быть включен. |
|`token`            |`string`         |Токен доступа AAD, который Kusto направит в конечную точку Для проверки подлинности. При установке строка подключения S'L не `Authentication`должна `User ID`включать `Password`информацию о проверке подлинности, как, или .|

## <a name="limitations-and-restrictions"></a>ограничения

При экспорте данных в базу данных СЗЛ существует ряд ограничений и ограничений:

1. Kusto — это облачный сервис, поэтому строка соединения должна указывать на базу данных, доступную из облака. (В частности, нельзя экспортировать в локтую базу данных, так как она недоступна из общедоступного облака.)

2. Kusto поддерживает Активную интегрированную аутентификацию каталога, когда`aaduser=` `aadapp=`директор вызова является основным принципом Active Directory Azure (или ).
   Кроме того, Kusto также поддерживает предоставление учетных данных для базы данных S'L как часть строки соединения. Другие методы проверки подлинности не поддерживаются. Идентификация, представленная в базе данных S'L, всегда исходит от вызывающего абонента команды, а не от самого удостоверения службы Kusto.

3. Если целевая таблица в базе данных S'L существует, она должна соответствовать схеме результата запроса. Обратите внимание, что в некоторых случаях (например, база данных Azure S'L) это означает, что в таблице есть одна колонка, отмеченная как столбец идентификации.

4. Экспорт больших объемов данных может занять много времени. Рекомендуется установить целевую таблицу S-L для минимальной регистрации во время массового импорта.
   См [> базы данных сервера s'L Server... > особенности базы данных > массовый импорт и экспорт данных.](https://msdn.microsoft.com/library/ms190422.aspx)

5. Экспорт данных осуществляется с использованием массовой копии S'L и не дает никаких транзакционных гарантий в целевой базе данных S'L. Более подробную информацию можно узнать из [транзакций и операций по копированию.](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/transaction-and-bulk-copy-operations)

6. Название таблицы S'L ограничено именем, состоящим из букв,`_`цифр,`.`пробелов, подчеркнуто`-`(), точек () и дефисов ().

7. Строка подключения S'L ограничена следующим `Persist Security Info` образом: `Encrypt` явно `true`установлена `Trust Server Certificate` `false`на, `false`установлена на.

8. При создании новой таблицы S'L можно указать основное свойство ключа в столбце. Если столбец тип, `string`то S'L может отказаться от создания таблицы из-за других ограничений на основной столбец ключа. Перед экспортом данных необходимо вручную создать таблицу в S'L. Причина этого ограничения заключается в том, что основные ключевые столбцы в S'L не могут быть неограниченного размера, но столбцы таблиц Kusto не имеют заявленных ограничений размера.

## <a name="azure-db-aad-integrated-authentication-documentation"></a>Интегрированная документация по аутентификации Azure DB AAD

* [Используйте активную аутентификацию каталогов Azure для проверки подлинности с помощью базы данных S'L](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)
* «Расширения аутентификации Azure AD для инструментов Azure S'L DB и S'L DW» (https://azure.microsoft.com/blog/azure-ad-authentication-extensions-for-azure-sql-db-and-sql-dw-tools/)

**Примеры** 

В этом примере Kusto запускает запрос, а затем экспортирует первый `MySqlTable` набор `MyDatabase` записей, `myserver`установленных запросом, в таблицу в базе данных на сервере.

```kusto 
.export async to sql MySqlTable
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    <| print Id="d3b68d12-cbd3-428b-807f-2c740f561989", Name="YSO4", DateOfBirth=datetime(2017-10-15)
```

В этом примере Kusto запускает запрос, а затем экспортирует первый `MySqlTable` набор `MyDatabase` записей, `myserver`установленных запросом, в таблицу в базе данных на сервере.
Если целевая таблица не существует в целевой базе данных, она создается.

```kusto 
.export async to sql ['dbo.MySqlTable']
    h@"Server=tcp:myserver.database.windows.net,1433;Database=MyDatabase;Authentication=Active Directory Integrated;Connection Timeout=30;"
    with (createifnotexists="true", primarykey="Id")
    <| print Message = "Hello World!", Timestamp = now(), Id=12345678
```