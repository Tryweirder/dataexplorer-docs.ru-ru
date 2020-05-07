---
title: Управление или подавление трассировки на стороне клиента пакета SDK Kusto в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается, как управлять или подавлять трассировку на стороне клиента пакета SDK Kusto в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 10/23/2018
ms.openlocfilehash: cbda69063e3b1a20549dbadb4641fc9fd3f51f57
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862060"
---
# <a name="controlling-or-suppressing-kusto-sdk-client-side-tracing"></a>Контроль или запрещение трассировки на стороне клиента пакета SDK Kusto

Клиентские библиотеки Kusto используют общую платформу для трассировки. Платформа использует большое количество источников трассировки (`System.Diagnostics.TraceSource`), каждое из которых подключено к набору прослушивателей трассировки по умолчанию (`System.Diagnostics.Trace.Listeners`) во время его создания.

Одно из этих значений заключается в том, что если приложение имеет прослушиватели трассировки, связанные `System.Diagnostics.Trace` с экземпляром по умолчанию ( `app.config` например, через файл), то клиентские библиотеки Kusto будут выдавать трассировки в эти прослушиватели.

Это поведение можно подавлять программно или контролировать с помощью файла конфигурации.

## <a name="suppress-tracing-programmatically"></a>Программное отключение трассировки

Чтобы программно отключить трассировку в клиентских библиотеках Kusto, перед загрузкой соответствующей библиотеки вызовите следующий фрагмент кода.

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="suppressing-tracing-by-using-a-config-file"></a>Подавление трассировки с помощью файла конфигурации

Чтобы отключить трассировку в клиентских библиотеках Kusto с помощью файла конфигурации, измените файл `Kusto.Cloud.Platform.dll.tweaks` (который входит в состав `Kusto.Data` библиотеки), чтобы подходящая Настройка стала доступна для чтения.

```xml
    <!-- Overrides the default trace verbosity level -->
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

(Обратите внимание, что для того, чтобы изменения вступили в силу, необходимо не использовать знак минус `key`в значении.)

Альтернативный способ — сделать следующее:

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="how-to-enable-the-kusto-client-libraries-tracing"></a>Включение трассировки клиентских библиотек Kusto

Чтобы включить трассировку из клиентских библиотек Kusto, включите трассировку .NET в файле App. config приложения. Например, предположим, что приложение `MyApp.exe` использует клиентскую библиотеку Kusto. Data. `MyApp.exe.config` Затем при следующем запуске приложения будет включена трассировка Kusto. Data.

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

При этом будет настроен прослушиватель трассировки, который записывает данные в CSV-файлы во `RollingLogs` вложенном каталоге, который называется, расположенном в каталоге Process. (Конечно, Any. Также можно использовать NET-совместимый класс прослушивателя трассировки.)

## <a name="how-to-enable-the-aad-client-libraries-adal-tracing"></a>Включение трассировки клиентских библиотек AAD (ADAL)

После того как трассировка для клиентских библиотек Kusto включена, трассировка, генерируемая клиентскими библиотеками AAD (клиентские библиотеки Kusto автоматически настраивают трассировку ADAL).
