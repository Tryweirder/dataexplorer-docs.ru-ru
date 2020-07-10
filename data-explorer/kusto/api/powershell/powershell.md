---
title: Клиентские библиотеки Kusto .NET из PowerShell — Azure обозреватель данных
description: В этой статье описывается использование клиентских библиотек .NET из PowerShell в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 6804b71ff3985de17460dddfa60f081f3bb910c0
ms.sourcegitcommit: b286703209f1b657ac3d81b01686940f58e5e145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86188427"
---
# <a name="using-the-net-client-libraries-from-powershell"></a>Использование клиентских библиотек .NET из PowerShell

Сценарии PowerShell могут использовать клиентские библиотеки Azure обозреватель данных .NET с помощью встроенной интеграции PowerShell с произвольными библиотеками .NET (не PowerShell).

## <a name="getting-the-net-client-libraries-for-scripting-with-powershell"></a>Получение клиентских библиотек .NET для создания сценариев с помощью PowerShell

Чтобы начать работу с клиентскими библиотеками Azure обозреватель данных .NET с помощью PowerShell.

1. Скачайте [ `Microsoft.Azure.Kusto.Tools` пакет NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/).
    * Если вы используете PowerShell 7 (или более позднюю версию), скачайте [ `Microsoft.Azure.Kusto.Tools.NETCore` пакет NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools.NETCore/).
1. Извлеките содержимое каталога Tools в пакете (используйте средство архивации, например `7-zip` ).
1. Вызовите `[System.Reflection.Assembly]::LoadFrom("path")` из PowerShell, чтобы загрузить необходимую библиотеку. 
    * `path`Параметр для команды должен указывать расположение извлеченных файлов.
1. После загрузки всех зависимых сборок .NET:
   1. Создайте строку подключения Kusto.
   1. Создайте экземпляр *поставщика запроса* или *поставщика администратора*.
   1. Выполните запросы или команды, как показано в [примерах](powershell.md#examples) ниже.

Дополнительные сведения см. в разделе [клиентские библиотеки Azure обозреватель данных](../netfx/about-kusto-data.md).

## <a name="examples"></a>Примеры

### <a name="initialization"></a>Инициализация

```powershell
#  Part 1 of 3
#  ------------
#  Packages location - This is an example of the location from where you extract the Microsoft.Azure.Kusto.Tools package.
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

#   Option A: using Azure AD User Authentication
$kcsb = New-Object Kusto.Data.KustoConnectionStringBuilder ($clusterUrl, $databaseName)
 
#   Option B: using Azure AD application Authentication
#     $applicationId = "application ID goes here"
#     $applicationKey = "application key goes here"
#     $authority = "authority goes here"
#     $kcsb = $kcsb.WithAadApplicationKeyAuthentication($applicationId, $applicationKey, $authority)
#
#   NOTE: if you're running with Powershell 7 (or above) and the .NET Core library,
#         AAD user authentication with prompt will not work, and you should choose
#         a different authentication method.
```

### <a name="example-running-an-admin-command"></a>Пример. выполнение команды администратора

```powershell
$adminProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslAdminProvider($kcsb)
$command = [Kusto.Data.Common.CslCommandGenerator]::GenerateDiagnosticsShowCommand()
Write-Host "Executing command: '$command' with connection string: '$($kcsb.ToString())'"
$reader = $adminProvider.ExecuteControlCommand($command)
$reader.Read() # this reads a single row/record. If you have multiple ones returned, you can read in a loop 
$isHealthy = $Reader.GetBoolean(0)
Write-Host "IsHealthy = $isHealthy"
```

Выходные данные:
```
IsHealthy = True
```

### <a name="example-running-a-query"></a>Пример. выполнение запроса

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

Выходные данные:

|StartTime           |EndTime             |еписодеид |EventID |Состояние          |EventType         |инжуриесдирект |инжуриесиндирект |деассдирект |деассиндирект
|---------           |-------             |--------- |------- |-----          |---------         |-------------- |---------------- |------------ |--------------
|2007-12-30 16:00:00 |2007-12-30 16:05:00 |    11749 |  64588 |Грузия        |Шквалистый ветер |             0 |               0 |           0 |             0
|2007-12-20 07:50:00 |2007-12-20 07:53:00 |    12554 |  68796 |Миссисипи    |Шквалистый ветер |             0 |               0 |           0 |             0
|2007-09-29 08:11:00 |2007-09-29 08:11:00 |    11091 |  61032 |АТЛАНТИЧЕСКОЕ ЮГО |Spout воды       |             0 |               0 |           0 |             0
|2007-09-20 21:57:00 |2007-09-20 22:05:00 |    11078 |  60913 |Флорида        |Торнадо           |             0 |               0 |           0 |             0
|2007-09-18 20:00:00 |2007-09-19 18:00:00 |    11074 |  60904 |Флорида        |Тяжелая дождя        |             0 |               0 |           0 |             0
