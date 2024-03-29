---
title: Глубокие ссылки пользовательского интерфейса — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются глубокие ссылки пользовательского интерфейса в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 412e489365daabfdde7de8cd61e398100f4bc3ef
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337403"
---
# <a name="ui-deep-links"></a>Глубокие ссылки пользовательского интерфейса

REST API предоставляет функцию глубокой компоновки, которая позволяет HTTP- `GET` запросам перенаправлять вызывающий объект в средство пользовательского интерфейса. Например, можно создавать универсальный код ресурса (URI), который открывает средство Kusto. Explorer, выполняет автоматическую настройку для конкретного кластера и базы данных, запускает конкретный запрос и отображает его результаты пользователю.

Глубокие ссылки пользовательского интерфейса REST API:

* Кластер (обязательный) обычно определяется неявно, как служба, реализующая REST API, но которую можно переопределить, указав параметр запроса URI `uri` .

* База данных (необязательно) указывается в качестве первой и только фрагмента пути URI. База данных является обязательной для запросов и необязательна для команд управления.

* Команда запроса или элемента управления (необязательно) указывается с помощью параметра запроса URI `query` или параметра запроса URI `querysrc` (который указывает на веб-ресурс, содержащий запрос).
  `query` может использоваться в тексте самой команды запроса или элемента управления (в кодировке используется параметр HTTP-запроса). Кроме того, его можно использовать в кодировке Base64 для текста команды запроса или элемента управления (что позволяет сжимать длинные запросы, чтобы они соответствовали ограничениям по длине URI браузера по умолчанию).

* Имя подключения кластера (необязательно) указывается с помощью параметра запроса URI `name` .

* Средство пользовательского интерфейса указывается с помощью `web` необязательного параметра запроса URI.
  `web=0` Указывает классическое приложение Kusto. Explorer. `web=1` Указывает веб-приложение Kusto. Web.
  `web=2` — Это старая версия Kusto. веб-проводника (на основе Application Insights Analytics). `web=3` является Kusto... The с пустым профилем (не будут доступны ранее открытые вкладки или кластеры). Наконец, `web` параметр запроса можно заменить на, `saw=1` чтобы указать версию Kusto. Explorer, которая является общедоступной.

Вот несколько примеров для ссылок:

* `https://help.kusto.windows.net/`: Когда агент пользователя (например, браузер) выдает запрос, `GET /` он перенаправляется к средству пользовательского интерфейса по умолчанию, настроенному для выполнения запросов к `help` кластеру.
* `https://help.kusto.windows.net/Samples`: Когда агент пользователя (например, браузер) выдает запрос, `GET /Samples` он перенаправляется к средству пользовательского интерфейса по умолчанию, настроенному для выполнения запросов к `help` `Samples` базе данных кластера.
* `http://help.kusto.windows.net/Samples?query=StormEvents`: Когда пользователь (например, браузер) выдает запрос, `GET /Samples?query=StormEvents` он перенаправляется к средству пользовательского интерфейса по умолчанию, настроенному для отправки запросов к `help` `Samples` базе данных кластера, и выдачи `StormEvents` запроса.

> [!NOTE]
> Для блоков URI с глубокими ссылками не требуется проверка подлинности, поскольку проверка подлинности выполняется средством пользовательского интерфейса, используемым для перенаправления.
> Любой `Authorization` заголовок HTTP, если он указан, игнорируется.

> [!IMPORTANT]
> По соображениям безопасности средства пользовательского интерфейса не выполняют автоматически команды управления, даже если `query` `querysrc` они указаны в глубокой ссылке.

## <a name="deep-linking-to-kustoexplorer"></a>Глубокая компоновка с Kusto. Explorer

Этот REST API выполняет перенаправление, которое устанавливает и запускает клиентское средство Kusto. Explorer для настольных систем с специально созданными параметрами запуска, которые открывают подключение к определенному кластеру подсистемы Kusto и выполняют запрос к этому кластеру.

* Путь: `/` [*DatabaseName*']
* Переключател `GET`
* Строка запроса: `web=0`

> [!NOTE]
> Описание синтаксиса URI перенаправления для запуска Kusto. Explorer см. в разделе [глубокая компоновка с помощью Kusto. Explorer](../../tools/kusto-explorer-using.md#deep-linking-queries) .

## <a name="deep-linking-to-kustowebexplorer"></a>Глубокая компоновка с Kusto.

Этот REST API выполняет перенаправление в веб-приложение Kusto. Web.

* Путь: `/` [*DatabaseName*']
* Переключател `GET`
* Строка запроса: `web=1`

## <a name="specifying-the-query-or-control-command-in-the-uri"></a>Указание команды запроса или элемента управления в URI

Если указан параметр строки запроса URI `query` , он должен быть закодирован в соответствии с правилами HTML для кодирования строки запроса URI. Кроме того, текст команды запроса или элемента управления может быть сжат gzip, а затем закодирован с помощью кодировки Base64. Это позволяет отсылать более длинные запросы или управляющие команды (поскольку последний метод кодирования приводит к более коротким URI).

## <a name="specifying-the-query-or-control-command-by-indirection"></a>Указание команды запроса или элемента управления путем косвенного обращения

Если команда запроса или элемента управления имеет слишком большую длину, даже при кодировании с помощью gzip/Base64 превышена максимальная длина URI агента пользователя. Кроме того, предоставляется параметр строки запроса URI `querysrc` , а его значение представляет собой короткий URI, указывающий на веб-ресурс, содержащий запрос или текст команды элемента управления.

Например, это может быть универсальный код ресурса (URI) для файла, размещенного в хранилище BLOB-объектов Azure.

> [!NOTE]
> Если ссылка имеет ссылку на средство пользовательского интерфейса веб-приложения, веб-служба, предоставляющая команду запроса или управления (то есть служба, предоставляющая `querysrc` универсальный код ресурса), должна быть настроена для поддержки CORS для `dataexplorer.azure.com` .
>
> Кроме того, если для этой службы требуются сведения о проверке подлинности или авторизации, она должна быть предоставлена как часть самого URI.
>
> Например, если `querysrc` точки большого двоичного объекта в хранилище BLOB-объектов Azure, необходимо настроить учетную запись хранения для поддержки CORS и либо сделать сам большой двоичный объект общедоступным (чтобы его можно было скачать без утверждений безопасности), либо добавить соответствующие SAS для хранилища Azure в универсальный код ресурса (URI). Конфигурацию CORS можно выполнить из [портал Azure](https://portal.azure.com/) или из [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/).
> См. раздел [Поддержка CORS в службе хранилища Azure](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).