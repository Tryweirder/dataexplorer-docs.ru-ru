---
title: Библиотека клиентов Kusto - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается клиентская библиотека Kusto в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/15/2020
ms.openlocfilehash: 8a3ab9bb3727efdc80e1887ab802f2ad4e3c7cce
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502607"
---
# <a name="kusto-client-library"></a>Библиотека клиентов Kusto
    
Kusto Client SDK (Kusto.Data) предоставляет программный API, подобный ADO.NET, поэтому использование его должно чувствовать себя естественно для тех, кто имеет опыт работы с .NET. Вы создаете либо клиента`ICslQueryProvider`запроса ()`ICslAdminProvider`или поставщика команд управления () из объекта строки соединения, указывающего на службу движка Kusto, базу данных, метод проверки подлинности и т.д. Затем можно выпустить запросы данных или команды управления, указав соответствующую строку языка запроса Kusto, и получить обратно одну или несколько таблиц данных через возвращенный `IDataReader` объект.

Более конкретно, чтобы создать ADO.NET, как клиент позволяет запросы против Кусто, использовать статические методы на класс. `Kusto.Data.Net.Client.KustoClientFactory` Они берут строку соединения и создают поток-безопасный, одноразовый, клиентский объект. (Настоятельно рекомендуется, чтобы клиентский код не создавал "слишком много" экземпляров этого объекта. Вместо этого клиентский код должен создать объект на строку подключения и удерживать его до тех пор, пока это необходимо.) Это позволяет объекту клиента эффективно кэшировать ресурсы.

Как правило, все методы на клиентах безопасны для `Dispose`потоков с двумя исключениями: и свойствами сеттера. Для последовательных результатов не ссылайтесь ни на один из методов одновременно.

Ниже приводятся несколько примеров. Дополнительные образцы можно найти [здесь](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client).

**Пример: Подсчет строк**
 
Следующий код демонстрирует подсчет строк таблицы, `StormEvents` названной `Samples`в базе данных под названием:

```csharp
var client = Kusto.Data.Net.Client.KustoClientFactory.CreateCslQueryProvider("https://help.kusto.windows.net/Samples;Fed=true");
var reader = client.ExecuteQuery("StormEvents | count");
// Read the first row from reader -- it's 0'th column is the count of records in MyTable
// Don't forget to dispose of reader when done.
```

**Пример: Получение информации о диагностике из кластера Kusto**

```csharp
var kcsb = new KustoConnectionStringBuilder(cluster URI here). WithAadUserPromptAuthentication();
using (var client = KustoClientFactory.CreateCslAdminProvider(kcsb))
{
    var diagnosticsCommand = CslCommandGenerator.GenerateShowDiagnosticsCommand();
    var objectReader = new ObjectReader<DiagnosticsShowCommandResult>(client.ExecuteControlCommand(diagnosticsCommand));
    DiagnosticsShowCommandResult diagResult = objectReader.ToList().FirstOrDefault();
    // DO something with the diagResult    
}
```



## <a name="the-kustoclientfactory-client-factory"></a>Клиентская фабрика KustoClientFactory

Статический `Kusto.Data.Net.Client.KustoClientFactory` класс обеспечивает основную точку входа для авторов клиентского кода, использующему Kusto. Он обеспечивает следующие важные статические методы:

|Метод                                      |Результаты                                |Используется для                                                      |
|--------------------------------------------|---------------------------------------|--------------------------------------------------------------|
|`CreateCslQueryProvider`                    |`ICslQueryProvider`                    |Отправка запросов в кластер двигателей Kusto.                    |
|`CreateCslAdminProvider`                    |`ICslAdminProvider`                    |Отправка команд управления в кластер Kusto (любого рода).    |
|`CreateRedirectProvider`                    |`IRedirectProvider`                    |Создание перенаправления сообщения от ответа HTTP для запроса Kusto.|

