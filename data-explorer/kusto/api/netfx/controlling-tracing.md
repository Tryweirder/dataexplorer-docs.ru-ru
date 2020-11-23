---
title: Kusto управления или отключение трассировки на стороне клиента SDK — Azure обозреватель данных
description: В этой статье описывается управление и подавление трассировки на стороне клиента Kusto SDK в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 10/23/2018
ms.openlocfilehash: 2224fe28c7f0088ac1a16cdee4d452e354ff0800
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324760"
---
# <a name="controlling-and-suppressing-kusto-sdk-client-side-tracing"></a>Контроль и подавление трассировки на стороне клиента пакета SDK Kusto

Клиентские библиотеки Kusto используют общую платформу для трассировки. Платформа использует большое количество источников трассировки ( `System.Diagnostics.TraceSource` ), и каждый из них подключается к набору прослушивателей трассировки () по умолчанию `System.Diagnostics.Trace.Listeners` во время его создания.

Если приложение имеет прослушиватели трассировки, связанные с экземпляром по умолчанию `System.Diagnostics.Trace` (например, с помощью `app.config` файла), то клиентские библиотеки Kusto будут создавать трассировки для этих прослушивателей.

Трассировку можно подавлять или контролировать программно или с помощью файла конфигурации.

## <a name="suppress-tracing-programmatically"></a>Программное отключение трассировки

Чтобы программно отключить трассировку в клиентских библиотеках Kusto, вызовите этот фрагмент кода при загрузке соответствующей библиотеки:

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="use-a-config-file-to-suppress-tracing"></a>Использование файла конфигурации для подавления трассировки 

Чтобы отключить трассировку в клиентских библиотеках Kusto с помощью файла конфигурации, измените файл `Kusto.Cloud.Platform.dll.tweaks` (который входит в `Kusto.Data` библиотеку).

```xml
    //Overrides the default trace verbosity level
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

> [!NOTE]
> Чтобы изменения вступили в силу, не должен быть знак "минус" в значении `key`

Альтернативой является:

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="enable-the-kusto-client-libraries-tracing"></a>Включение трассировки клиентских библиотек Kusto

Чтобы включить трассировку из клиентских библиотек Kusto, включите трассировку .NET в *файлеapp.config* приложения. Например, предположим, что приложение `MyApp.exe` использует клиентскую библиотеку Kusto. Data. Изменение *MyApp.exe.config* файла для включения следующих изменений включит `Kusto.Data` трассировку при следующем запуске приложения.

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

В коде будет настроен прослушиватель трассировки, который записывает данные в CSV-файлы в подкаталог с именем *роллинглогс*. Подкаталог находится в каталоге Process.

> [!NOTE]
> Всеми. Также можно использовать NET-совместимый класс прослушивателя трассировки

## <a name="enable-the-azure-ad-client-libraries-adal-tracing"></a>Включение трассировки клиентских библиотек Azure AD (ADAL)

После включения трассировки для клиентских библиотек Kusto это будет трассировка клиентских библиотек Azure AD. Клиентские библиотеки Kusto автоматически настраивают трассировку ADAL.
