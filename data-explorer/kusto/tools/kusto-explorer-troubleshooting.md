---
title: Устранение распространенных неполадок в Kusto. Explorer
description: Сведения об общих проблемах при установке и запуске Kusto. Explorer и их решениях
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 9a697cfd37590f0368d5a8f0bacf91d02e1c8725
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "92003153"
---
# <a name="troubleshooting"></a>Диагностика

Этот документ содержит распространенные проблемы при запуске и использовании Kusto. Explorer, а также предлагает решения. В этом документе также описано [, как сбросить Kusto. Explorer](#reset-kustoexplorer).

## <a name="kustoexplorer-fails-to-start"></a>Не удается запустить Kusto. Explorer

### <a name="kustoexplorer-shows-error-dialog-during-or-after-start-up"></a>Kusto. Explorer отображает диалоговое окно ошибки во время или после запуска

#### <a name="symptom"></a>Симптом

При запуске Kusto. Explorer отображает `InvalidOperationException` ошибку.

#### <a name="possible-solution"></a>Возможное решение

Эта ошибка может означать, что операционная система повреждена или в ней отсутствуют некоторые из необходимых модулей.
Чтобы проверить отсутствующие или поврежденные системные файлы, выполните описанные здесь действия.   
[https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system](https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system)

## <a name="kustoexplorer-always-downloads-even-when-there-are-no-updates"></a>Kusto. Explorer всегда скачивается даже при отсутствии обновлений

#### <a name="symptom"></a>Симптом

При каждом открытии Kusto. Explorer вам будет предложено установить новую версию. Kusto. Explorer скачивает весь пакет, не обновляя уже установленную версию.

#### <a name="possible-solution"></a>Возможное решение

Этот симптом может быть результатом повреждения в локальном хранилище ClickOnce. Вы можете очистить локальное хранилище ClickOnce, выполнив следующую команду в командной строке с повышенными привилегиями.

> [!IMPORTANT]
> 1. Если имеются другие экземпляры приложений ClickOnce или `dfsvc.exe` , завершите их перед выполнением этой команды.
> 1. Все приложения ClickOnce будут переустановлены автоматически при следующем запуске, если у вас есть доступ к исходному расположению установки, сохраненному в ярлыке приложения. Ярлыки приложений удаляться не будут.

```kusto
rd /q /s %userprofile%\appdata\local\apps\2.0
```

Попробуйте снова установить Kusto. Explorer с одного из [зеркал установки](kusto-explorer.md#installing-kustoexplorer).

### <a name="clickonce-error-cannot-start-application"></a>Ошибка ClickOnce: не удается запустить приложение

#### <a name="symptoms"></a>Симптомы

Программа не запускается и выводит одну из следующих ошибок: 
* `External component has thrown an exception`
* `Value does not fall within the expected range`
* `The application binding data format is invalid.` 
* `Exception from HRESULT: 0x800736B2`
* `The referenced assembly is not installed on your system. (Exception from HRESULT: 0x800736B3)`

Сведения об ошибке можно просмотреть, щелкнув `Details` следующее диалоговое окно ошибки:

![Ошибка ClickOnce](./Images/kusto-explorer-troubleshooting/clickonce-err-1.png)

```kusto
Following errors were detected during this operation.
    * System.ArgumentException
        - Value does not fall within the expected range.
        - Source: System.Deployment
        - Stack trace:
            at System.Deployment.Application.NativeMethods.CorLaunchApplication(UInt32 hostType, String applicationFullName, Int32 manifestPathsCount, String[] manifestPaths, Int32 activationDataCount, String[] activationData, PROCESS_INFORMATION processInformation)
            at System.Deployment.Application.ComponentStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.SubscriptionStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.Activate(DefinitionAppId appId, AssemblyManifest appManifest, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.ProcessOrFollowShortcut(String shortcutFile, String& errorPageUrl, TempFile& deployFile)
            at System.Deployment.Application.ApplicationActivator.PerformDeploymentActivation(Uri activationUri, Boolean isShortcut, String textualSubId, String deploymentProviderUrlFromExtension, BrowserSettings browserSettings, String& errorPageUrl)
            at System.Deployment.Application.ApplicationActivator.ActivateDeploymentWorker(Object state)
```

#### <a name="proposed-solution-steps"></a>Предлагаемые шаги решения

1. Удалите Kusto. Explorer с помощью `Programs and Features` ( `appwiz.cpl` ).

1. Попробуйте запустить `CleanOnlineAppCache` , а затем снова попробуйте установить Kusto. Explorer. 
    Из командной строки с повышенными привилегиями: 
    
    ```kusto
    rundll32 %windir%\system32\dfshim.dll CleanOnlineAppCache
    ```

    Снова установите Kusto. Explorer с одного из [зеркал установки](kusto-explorer.md#installing-kustoexplorer).

1. Если приложение по-прежнему не запускается, удалите локальное хранилище ClickOnce. Все приложения ClickOnce будут переустановлены автоматически при следующем запуске, если у вас есть доступ к исходному расположению установки, сохраненному в ярлыке приложения. Ярлыки приложений удаляться не будут.

    Из командной строки с повышенными привилегиями:

    ```kusto
    rd /q /s %userprofile%\appdata\local\apps\2.0
    ```

    Снова установите Kusto. Explorer на одном из [зеркал установки](kusto-explorer.md#installing-kustoexplorer) .

1. Если приложение по-прежнему не запускается:
    1. Удалите временные файлы развертывания.
    1. Переименуйте папку "Local AppData" Kusto. Explorer.

        Из командной строки с повышенными привилегиями:

        ```kusto
        rd /s/q %userprofile%\AppData\Local\Temp\Deployment
        ren %LOCALAPPDATA%\Kusto.Explorer Kusto.Explorer.bak
        ```

    1. Снова установите Kusto. Explorer на одном из [зеркал установки](kusto-explorer.md#installing-kustoexplorer) .

    1. Чтобы восстановить подключения из Kusto. Explorer. bak, в командной строке с повышенными привилегиями:

        ```kusto
        copy %LOCALAPPDATA%\Kusto.Explorer.bak\User*.xml %LOCALAPPDATA%\Kusto.Explorer
        ```

#### <a name="enabling-clickonce-verbose-logging"></a>Включение подробного ведения журнала ClickOnce

1. Если приложение по-прежнему не запускается:
    1. [Включите подробное ведение журнала ClickOnce](https://docs.microsoft.com/visualstudio/deployment/how-to-specify-verbose-log-files-for-clickonce-deployments) , создав строку логвербоситилевел со значением 1 в разделе:

        ```kusto
        HKEY_CURRENT_USER\Software\Classes\Software\Microsoft\Windows\CurrentVersion\Deployment
        ```
    
    1. Повторите воспроизведение.
    1. Отправьте подробные выходные данные в KEBugReport@microsoft.com . 

### <a name="clickonce-error-your-administrator-has-blocked-this-application-because-it-potentially-poses-a-security-risk-to-your-computer"></a>Ошибка ClickOnce: администратор заблокировал это приложение, так как оно потенциально создает угрозу безопасности для вашего компьютера

#### <a name="symptom"></a>Симптом 
Не удается установить приложение с одной из следующих ошибок:
* `Your administrator has blocked this application because it potentially poses a security risk to your computer`.
* `Your security settings do not allow this application to be installed on your computer.`

#### <a name="solution"></a>Решение

Это может быть вызвано тем, что другое приложение переопределяет поведение запроса о доверии ClickOnce по умолчанию. 
1. Просмотр параметров конфигурации по умолчанию.
1. Сравните параметры конфигурации с фактическими параметрами на вашем компьютере.
1. При необходимости сбросьте параметры конфигурации, как описано [в этой статье](https://docs.microsoft.com/visualstudio/deployment/how-to-configure-the-clickonce-trust-prompt-behavior).

### <a name="cleanup-application-data"></a>Очистка данных приложения

Иногда, если предыдущие шаги по устранению неполадок не помогут получить Kusto. Explorer для запуска, очистка данных, хранящихся локально, может помочь.

Данные, хранящиеся в приложении Kusto. Explorer, можно найти здесь: `C:\Users\[your username]\AppData\Local\Kusto.Explorer` .

> [!NOTE]
> Очистка данных приведет к утрате открытых вкладок (папки восстановления), сохраненных подключений (папки Connections) и параметров приложения (папка UserSettings).

## <a name="reset-kustoexplorer"></a>Сброс Kusto. Explorer

При необходимости можно полностью сбросить Kusto. Explorer. В следующей процедуре описано, как выполнить постепенное восстановление Kusto. Explorer, пока он не будет удален с компьютера и должен быть установлен с нуля.

1. В Windows откройте окно **изменение или удаление программы** (также известной как **программы и компоненты**).
1. Выберите каждый элемент, начинающийся с `Kusto.Explorer` .
1. Выберите **Удалить**.

   Если эта процедура не позволяет удалить приложение (известная проблема с приложениями ClickOnce), см. [инструкции в этой статье](https://stackoverflow.com/questions/10896223/how-do-i-completely-uninstall-a-clickonce-application-from-my-computer).

1. Удалите папку `%LOCALAPPDATA%\Kusto.Explorer` , которая удаляет все подключения, журнал и т. д.

1. Удалите папку `%APPDATA%\Kusto` , которая удаляет кэш маркеров Kusto. Explorer. Необходимо выполнить повторную проверку подлинности для всех кластеров.

Также можно вернуться к определенной версии Kusto. Explorer:

1. Выполните `appwiz.cpl`.
1. Выберите **Kusto. Explorer** и щелкните **Удалить/изменить**.
1. Выберите **восстановить предыдущее состояние приложения**.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о [пользовательском интерфейсе Kusto. Explorer](kusto-explorer.md#overview-of-the-user-interface)
* Сведения о [запуске Kusto. Explorer из командной строки](kusto-explorer-using.md#kustoexplorer-command-line-arguments)
* Дополнительные сведения о [языке запросов Kusto (ККЛ)](https://docs.microsoft.com/azure/kusto/query/)
