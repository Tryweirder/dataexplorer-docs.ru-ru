---
title: Контроль или подавление отслеживания клиентской стороны Kusto SDK - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается контроль или подавление отслеживания клиентской стороны Kusto SDK в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 65964d7e990cdb639bd5bfe319d11874ea3de15d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502743"
---
# <a name="controlling-or-suppressing-kusto-sdk-client-side-tracing"></a>Контроль или подавление отслеживания клиентской стороны Kusto SDK

Библиотеки клиентов Kusto используют общую платформу для отслеживания. Платформа использует большое количество источников трассировки (),`System.Diagnostics.TraceSource`каждый`System.Diagnostics.Trace.Listeners`из которых подключен к набору слушателей трассировки по умолчанию () во время его строительства.

Одним из следствий этого является то, что `System.Diagnostics.Trace` если приложение имеет слушатели трассировки, связанные с экземпляром по умолчанию (например, через его `app.config` файл), то библиотеки клиентов Kusto будут излучать следы для этих слушателей.

Такое поведение может быть подавлено или контролируется программно или через файл конфигурации.

## <a name="suppress-tracing-programmatically"></a>Подавление отслеживания программно

Чтобы подавить отслеживание из клиентских библиотек Kusto программно, при загрузке соответствующей библиотеки вызвать следующий фрагмент кода на ранних стадиях:

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="suppressing-tracing-by-using-a-config-file"></a>Подавление отслеживания с помощью файла конфигурации

Чтобы подавить отслеживание из библиотек клиентов Kusto через `Kusto.Cloud.Platform.dll.tweaks` файл конфигурации, `Kusto.Data` измените файл (который включен в библиотеку), так что соответствующий "твик" теперь гласит:

```xml
    <!-- Overrides the default trace verbosity level -->
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

(Обратите внимание, что для настройки, чтобы ввести в `key`действие не должно быть минус знак в значении .)

Альтернативным средством является следующее:

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="how-to-enable-the-kusto-client-libraries-tracing"></a>Как обеспечить отслеживание клиентских библиотек Kusto

Чтобы включить отслеживание из клиентских библиотек Kusto, включите трассировку .NET в файле app.config вашего приложения. Например, если предположить, что приложение `MyApp.exe` использует клиентскую библиотеку Kusto.Data. Затем изменение `MyApp.exe.config` файла для включения следующих данных позволит Kusto.Data отслеживать следующий раз, когда приложение начинается:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.diagnostics>
    <trace indentsize="4">
      <listeners>
        <add type="Kusto.Cloud.Platform.Utils.RollingCsvTraceListener2, Kusto.Cloud.Platform" name="RollingCsvTraceListener" initializeData="RollingLogs" />
        <remove name="Default" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
``` 

Это позволит настроить слушателя трассировки, который записывает в `RollingLogs` файлы CSV в подкаталоге, называемом расположенным в каталоге процесса. (Конечно, любой . NET-совместимый класс слушателя трассировки может быть использован также.) 

## <a name="how-to-enable-the-aad-client-libraries-adal-tracing"></a>Как обеспечить отслеживание клиентских библиотек AAD (ADAL)

После включения отслеживания клиентских библиотек Kusto можно отследить и отслеживание, излучаемые библиотеками клиентов AAD (библиотеки клиентов Kusto автоматически настраивают трассировку ADAL)

