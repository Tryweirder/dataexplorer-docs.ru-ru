---
title: Использование клиентских библиотек .NET от PowerShell - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается использование клиентских библиотек .NET от PowerShell в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 635a23021a1a8c30347bfa27ecd65886b46a6fea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503219"
---
# <a name="using-the-net-client-libraries-from-powershell"></a>Использование клиентских библиотек .NET от PowerShell

Клиентские библиотеки Azure Data Explorer .NET могут использоваться скриптами PowerShell через встроенную интеграцию PowerShell с произвольными (не PowerShell) .NET библиотеками.

## <a name="getting-the-net-client-libraries-for-scripting-with-powershell"></a>Получение библиотек клиентов .NET для скриптов с PowerShell

Чтобы начать работу с библиотеками клиентов Azure Data Explorer .NET с помощью PowerShell:

1. Скачать [ `Microsoft.Azure.Kusto.Tools` пакет NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/).
2. Извлеките содержимое каталога «инструменты» в пакете (например, с помощью 7-zip).
3. Звоните `[System.Reflection.Assembly]::LoadFrom("path")` из Powershell, чтобы загрузить необходимую библиотеку. 
    - Параметр `"path"` команды должен указывать местоположение извлеченных файлов.
4. После загрузки всех зависимых сборок .NET создайте строку соединения Kusto, мгновенно запустите *поставщика запросов* или *поставщика админ-* и запустите запросы или команды (как показано в приведенных ниже [примерах).](powershell.md#examples)

Для получения [подробной](../netfx/about-kusto-data.md)информации см.

## <a name="examples"></a>Примеры

### <a name="initialization"></a>Инициализация

```powershell
#  Part 1 of 3
#  ------------
#  Packages location - This is an example to the location where you extract the Microsoft.Azure.Kusto.Tools package.
#  Please make sure you load the types from a local directory and not from a remote share.
$packagesRoot = "C:\Microsoft.Azure.Kusto.Tools\Tools"

#  Part 2 of 3
#  ------------
#  Loading the Kusto.Client library and its dependencies
dir $packagesRoot\* | Unblock-File
[System.Reflection.Assembly]::LoadFrom("$packagesRoot\Kusto.Data.dll")

#  Part 3 of 3
#  ------------
#  Defining the connection to your cluster / database
$clusterUrl = "https://help.kusto.windows.net;Fed=True"
$databaseName = "Samples"

#   Option A: using AAD User Authentication
$kcsb = New-Object Kusto.Data.KustoConnectionStringBuilder ($clusterUrl, $databaseName)
 
#   Option B: using AAD application Authentication
#     $applicationId = "application ID goes here"
#     $applicationKey = "application key goes here"
#     $authority = "authority goes here"
#     $kcsb = $kcsb.WithAadApplicationKeyAuthentication($applicationId, $applicationKey, $authority)
```

### <a name="example-running-an-admin-command"></a>Пример: Запуск команды админ-айда

```powershell
$adminProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslAdminProvider($kcsb)
$command = [Kusto.Data.Common.CslCommandGenerator]::GenerateDiagnosticsShowCommand()
Write-Host "Executing command: '$command' with connection string: '$($kcsb.ToString())'"
$reader = $adminProvider.ExecuteControlCommand($command)
$reader.Read() # this reads a single row/record. If you have multiple ones returned, you can read in a loop 
$isHealthy = $Reader.GetBoolean(0)
Write-Host "IsHealthy = $isHealthy"
```

И выход:
```
IsHealthy = True
```

### <a name="example-running-a-query"></a>Пример: Запуск запроса

```powershell
$queryProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslQueryProvider($kcsb)
$query = "StormEvents | limit 5"
Write-Host "Executing query: '$query' with connection string: '$($kcsb.ToString())'"
#   Optional: set a client request ID and set a client request property (e.g. Server Timeout)
$crp = New-Object Kusto.Data.Common.ClientRequestProperties
$crp.ClientRequestId = "MyPowershellScript.ExecuteQuery." + [Guid]::NewGuid().ToString()
$crp.SetOption([Kusto.Data.Common.ClientRequestProperties]::OptionServerTimeout, [TimeSpan]::FromSeconds(30))

#   Execute the query
$reader = $queryProvider.ExecuteQuery($query, $crp)

# Do something with the result datatable, for example: print it formatted as a table, sorted by the 
# "StartTime" column, in descending order
$dataTable = [Kusto.Cloud.Platform.Data.ExtendedDataReader]::ToDataSet($reader).Tables[0]
$dataView = New-Object System.Data.DataView($dataTable)
$dataView | Sort StartTime -Descending | Format-Table -AutoSize
```

И выход:

|StartTime           |EndTime             |ЭпизодИд |EventId |Состояние          |EventType         |ТравмыПрямая |ТравмыКосвенные |DeathsDirect |СмертиКосвенные смерти
|---------           |-------             |--------- |------- |-----          |---------         |-------------- |---------------- |------------ |--------------
|2007-12-30 16:00:00 |2007-12-30 16:05:00 |    11749 |  64588 |Грузия        |Шквалистый ветер |             0 |               0 |           0 |             0
|2007-12-20 07:50:00 |2007-12-20 07:53:00 |    12554 |  68796 |Миссисипи    |Шквалистый ветер |             0 |               0 |           0 |             0
|2007-09-29 08:11:00 |2007-09-29 08:11:00 |    11091 |  61032 |АТЛАНТИЧЕСКИЙ ЮГ |Водяной сот        |             0 |               0 |           0 |             0
|2007-09-20 21:57:00 |2007-09-20 22:05:00 |    11078 |  60913 |Флорида        |Торнадо           |             0 |               0 |           0 |             0
|2007-09-18 20:00:00 |2007-09-19 18:00:00 |    11074 |  60904 |Флорида        |Сильный дождь        |             0 |               0 |           0 |             0