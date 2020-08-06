---
title: Использование Kusto.Explorer
description: Узнайте, как использовать Kusto. Explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 8688c7d6f2a5d68e0e1ae4436e099b011fd5faa0
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803239"
---
# <a name="using-kustoexplorer"></a>Использование Kusto.Explorer

Kusto. Explorer — это классическое приложение, позволяющее исследовать данные с помощью языка запросов Kusto в удобном для использования пользовательском интерфейсе. В этой статье показано, как использовать режимы поиска и запросов, совместно использовать запросы и управлять кластерами, базами данных и таблицами.

## <a name="search-mode"></a>Режим поиска + +

Режим поиска + + позволяет искать термин с помощью синтаксиса поиска в одной или нескольких таблицах.

1. На вкладке Главная в раскрывающемся списке запрос выберите **Поиск + +**.
1. Выберите **несколько таблиц**.
1. В разделе **Выбор таблиц**Укажите таблицы для поиска.
1. В поле ввода введите фразу для поиска и выберите **Go (перейти**).
1. Тепловая схема сетки таблицы или временного слота показывает, какие термины отображаются и где они отображаются.

    :::image type="content" source="images/kusto-explorer-using/search-plus-plus.png" alt-text="Поиск + + Kusto Explorer":::

1. Выберите ячейку в сетке и выберите **Просмотреть сведения** , чтобы отобразить соответствующие записи в области результатов.

    :::image type="content" source="images/kusto-explorer-using/search-plus-plus-results.png" alt-text="Результаты поиска в Kusto Explorer + +":::

## <a name="query-mode"></a>режим запроса

Kusto. Explorer включает в себя мощный режим работы со сценариями, позволяющий писать, изменять и выполнять специальные запросы. Режим скриптов поставляется с выделением синтаксиса и технологией IntelliSense, что позволяет быстро освоить знания языка запросов Kusto.

В этом разделе описывается, как выполнять базовые запросы в Kusto. Explorer и как добавлять параметры в запросы.

## <a name="basic-queries"></a>Базовые запросы

Если у вас есть журналы таблиц, их можно приступить к изучению:

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
StormEvents | count 
```

Когда курсор находится на этой строке, он окрашивается серым цветом. Нажмите клавишу **F5** , чтобы выполнить запрос. 

Вот несколько примеров запросов:

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
// Take 10 lines from the table. Useful to get familiar with the data
StormEvents | limit 10 
```

<!-- csl: https://help.kusto.windows.net:443/Samples -->

```kusto
// Filter by EventType == 'Flood' and State == 'California' (=~ means case insensitive) 
// and take sample of 10 lines
StormEvents 
| where EventType == 'Flood' and State =~ 'California'
| limit 10
```

:::image type="content" source="images/kusto-explorer-using/basic-query.png" alt-text="Базовый запрос Kusto Explorer":::

Дополнительные сведения о [языке запросов Kusto](https://docs.microsoft.com/azure/kusto/query/).

> [!NOTE]
> Пустые строки в выражении запроса могут влиять на то, какая часть запроса выполняется.
>
> Если текст не выбран, предполагается, что запрос или команда разделены пустыми строками.
> Если текст выбран, будет выполнен выделенный текст.

## <a name="client-side-query-parameterization"></a>Параметризация запросов на стороне клиента

> [!NOTE]
> В Kusto существует два типа методов параметризатион запросов:
> * [Встроенный в язык запрос параметризатион](../query/queryparametersstatement.md) реализуется как часть механизма обработки запросов и предназначен для использования приложениями, которые запрашивают службу программным способом. Этот метод не описан в этом документе.
>
> * Параметризатион запросов на стороне клиента, описанный ниже, является компонентом только приложения Kusto. Explorer. Он эквивалентен использованию операций String-Replace в запросах перед их отправкой для выполнения службой. Синтаксис, описанный ниже, не является частью самого языка запросов и не может использоваться при отправке запросов к службе средствами, отличными от Kusto. Explorer.

Если одно и то же значение используется в нескольких запросах или на нескольких вкладках, очень неудобно изменить это значение в каждом месте, где оно используется. Именно поэтому Kusto. Explorer поддерживает параметры запроса. Параметры запроса являются общими для всех вкладок, поэтому их можно легко использовать повторно. Параметры обозначаются {} квадратными скобками. Пример: `{parameter1}`

В редакторе скриптов выделены параметры запроса:

:::image type="content" source="images/kusto-explorer-using/parametrized-query-1.png" alt-text="Запрос параметризованным 1":::

Можно легко определить и изменить существующие параметры запроса:


:::image type="content" source="images/kusto-explorer-using/parametrized-query-2.png" alt-text="Изменить запрос параметризованным 2":::


:::image type="content" source="images/kusto-explorer-using/parametrized-query-3.png" alt-text="Изменить запрос параметризованным 3":::

В редакторе скриптов также используется IntelliSense для уже определенных параметров запроса:

:::image type="content" source="images/kusto-explorer-using/parametrized-query-4.png" alt-text="Параматеризед запрос IntelliSense":::

У вас может быть несколько наборов параметров (перечисленных в поле со списком « **набор параметров** »).
Выберите **Добавить новый** или **Удалить текущий** , чтобы управлять списком наборов параметров.

:::image type="content" source="images/kusto-explorer-using/parametrized-query-5.png" alt-text="Список наборов параметров":::

## <a name="share-queries-and-results"></a>Совместное использование запросов и результатов

В Kusto. Explorer можно обмениваться запросами и результатами по электронной почте. Кроме того, можно создавать глубокие ссылки, которые открывают и запускают запрос в браузере.

### <a name="share-queries-and-results-by-email"></a>Предоставление общего доступа к запросам и результатам по электронной почте

Kusto. Explorer предоставляет удобный способ обмена запросами и результатами запросов по электронной почте.

1. [Выполните запрос](#basic-queries) в Kusto. Explorer.
1. На вкладке Главная в разделе Общий доступ выберите **Экспорт в буфер обмена** (или нажмите клавиши CTRL + SHIFT + C).

    :::image type="content" source="images/kusto-explorer-using/menu-export.png" alt-text="Экспорт в буфер обмена":::

    Kusto. Explorer вставит следующий фрагмент в буфер обмена:
     * Ваш запрос
     * Результаты запроса (таблица или диаграмма)
     * Сведения о подключении для кластера и базы данных Kusto
     * Ссылка для автоматического повторного запуска запроса

1. Вставка содержимого буфера обмена в новое сообщение электронной почты.

    :::image type="content" source="images/kusto-explorer-using/share-results-2.png" alt-text="Поделиться результатами по электронной почте":::

### <a name="deep-linking-queries"></a>Запросы с глубокими привязками

Можно создать URI, который при открытии в браузере открывает Kusto. Explorer локально и выполняет определенный запрос к указанной базе данных Kusto.

> [!NOTE] 
> По соображениям безопасности глубокая компоновка отключена для команд управления.

#### <a name="creating-a-deep-link"></a>Создание глубокой компоновки

Самый простой способ создать глубокую компоновку — это создание запроса в Kusto. Explorer, а затем использование `Export to Clipboard` для копирования запроса (включая глубокую ссылку и результаты) в буфер обмена. Затем вы можете поделиться им по электронной почте.
        
При копировании в электронное письмо прямая ссылка отображается мелким шрифтом. Пример:

https://help.kusto.windows.net:443/Samples[[Щелкните, чтобы запустить запрос](https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d)] 

Первая ссылка открывает Kusto. Explorer и устанавливает правильный контекст кластера и базы данных.
Вторая ссылка ( `Click to run query` ) — это прямая ссылка. При перемещении ссылки на сообщение электронной почты и нажатии клавиш CTRL + K можно увидеть фактический URL-адрес:

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

#### <a name="deep-links-and-parametrized-queries"></a>Запросы с глубокими ссылками и параметризованным

Вы можете использовать запросы параметризованным с глубоким связыванием.

1. Создайте запрос, который будет сформирован как запрос параметризованным (например,). `KustoLogs | where Timestamp > ago({Period}) | count` 
1. Укажите параметр для каждого параметра запроса в URI, например: 
    
    `https://<your_cluster>.kusto.windows.net/MyDatabase?
web=0&query=KustoLogs+%7c+where+Timestamp+>+ago({Period})+%7c+count&Period=1h`

    Замените &lt; your_cluster &gt; именем кластера Azure обозреватель данных.

#### <a name="limitations"></a>Ограничения

Длина запросов ограничена ~ 2000 символами из-за ограничений браузера, прокси HTTP и средств, проверяющих связи, например Microsoft Outlook. Ограничение является приблизительным, так как оно зависит от длины имени кластера и базы данных. Дополнительные сведения см. в статье [https://support.microsoft.com/kb/208427](https://support.microsoft.com/kb/208427). 

Чтобы уменьшить вероятность достижения предельного числа символов, см. статью о том, как [получить более короткие ссылки](#getting-shorter-links).

Формат URI:`https://<ClusterCname>.kusto.windows.net/<DatabaseName>web=0?query=<QueryToExecute>`

Например:[https://help.kusto.windows.net/Samples?web=0query=StormEvents+%7c+limit+10](https://help.kusto.windows.net/Samples?web=0query=StormEvents+%7c+limit+10)
 
Этот универсальный код ресурса (URI) откроет Kusto. Explorer, подключится к `Help` кластеру Kusto и запустит указанный запрос к `Samples` базе данных. Если экземпляр Kusto. Explorer уже запущен, запущенный экземпляр откроет новую вкладку и запустит в ней запрос.

### <a name="getting-shorter-links"></a>Получение более коротких ссылок

Запросы могут стать длинными. Чтобы уменьшить вероятность превышения предельной длины запроса, используйте `String Kusto.Data.Common.CslCommandGenerator.EncodeQueryAsBase64Url(string query)` метод, доступный в клиентской библиотеке Kusto. Этот метод создает более компактную версию запроса. Более короткий формат также распознается Kusto. Explorer.

https://help.kusto.windows.net/Samples?web=0&query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVuDlqlEoLs3NTSzKrEpVSM4vzSvR0FRIqlRIyszTCC5JLCoJycxN1VEwT9EEKS1KzUtJLVIoAYolZwAlFQCB3oo%2bTAAAAA%3d%3d

Запрос становится более компактным, применяя следующее преобразование:

```csharp
 UrlEncode(Base64Encode(GZip(original query)))
```

## <a name="kustoexplorer-command-line-arguments"></a>Аргументы командной строки Kusto. Explorer

Аргументы командной строки используются для настройки средства для выполнения дополнительных функций при запуске. Например, загрузите сценарий и подключитесь к кластеру. Таким образом, аргументы командной строки не заменяют функции Kusto. Explorer.

Аргументы командной строки передаются как часть URL-адреса, используемого для открытия приложения, таким же образом, как и [запрос на глубокую компоновку](#creating-a-deep-link).

### <a name="command-line-argument-syntax"></a>Синтаксис аргументов командной строки

Kusto. Explorer поддерживает несколько аргументов командной строки в следующем синтаксисе (порядок важен):

[*Локалскриптфиле*] [*Строка запроса*]

* *Локалскриптфиле* — имя файла скрипта на локальном компьютере, который должен иметь расширение `.kql` . Если такой файл существует, Kusto. Explorer автоматически загружает этот файл при запуске.
* *QueryString* — это строка, использующая форматирование строки запроса HTTP. Этот метод предоставляет дополнительные свойства, как описано в таблице ниже.

Например, чтобы запустить Kusto. Explorer с файлом скрипта с именем `c:\temp\script.kql` и настроить для взаимодействия с кластером `help` , базу данных `Samples` , используйте следующую команду:

```kusto
Kusto.Explorer.exe c:\temp\script.kql uri=https://help.kusto.windows.net/Samples;Fed=true&name=Samples
```

|Аргумент  |Описание                                                               |
|----------|--------------------------------------------------------------------------|
|**Запрос для выполнения**                                                                 |
|`query`   |Выполняемый запрос (в кодировке Base64). Если значение пустое, используйте `querysrc` .          |
|`querysrc`|URL-адрес файла или большого двоичного объекта, содержащего запрос для выполнения (если `query` пуст).|
|**Подключение к кластеру Kusto**                                                  |
|`uri`     |Строка подключения кластера Kusto для подключения.                 |
|`name`    |Отображаемое имя подключения к кластеру Kusto.                  |
|**Группа соединений**                                                                 |
|`path`    |URL-адрес загружаемого файла группы соединений (в кодировке URL).             |
|`group`   |Имя группы соединения.                                         |
|`filename`|Локальный файл, содержащий группу соединений.                              |


## <a name="manage-clusters-databases-tables-or-function-authorized-principals"></a>Управление кластерами, базами данных, таблицами и зарегистрированными участниками функции

> [!NOTE]
> Только [Администраторы](../management/access-control/role-based-authorization.md) могут добавлять или удалять полномочные субъекты в своей области.

Щелкните правой кнопкой мыши целевую сущность на [панели подключения](kusto-explorer.md#connections-tab)и выберите пункт **Управление полномочными участниками кластера**. (Этот параметр можно также выбрать в меню Управление.)

:::image type="content" source="images/kusto-explorer-using/right-click-manage-authorized-principals.png" alt-text="Управление полномочными участниками":::

:::image type="content" source="images/kusto-explorer-using/manage-authorized-principals-window.png" alt-text="Окно "Управление полномочными участниками"":::

* Чтобы добавить нового полномочного участника, выберите **Добавить субъект**, укажите сведения о субъекте и подтвердите действие.
    
    :::image type="content" source="images/kusto-explorer-using/add-authorized-principals-window.png" alt-text="Добавить полномочного участника":::

    :::image type="content" source="images/kusto-explorer-using/confirm-add-authorized-principals.png" alt-text="Подтверждение добавления полномочного участника":::

* Чтобы удалить существующего полномочного участника, выберите **Удалить субъект** и подтвердите действие.

    :::image type="content" source="images/kusto-explorer-using/confirm-drop-authorized-principals.png" alt-text="Подтверждение удаления полномочного участника":::


## <a name="next-steps"></a>Дальнейшие действия

* [Сочетания клавиш Kusto. Explorer](kusto-explorer-shortcuts.md)
* [Параметры Kusto.Explorer](kusto-explorer-options.md)
* [Устранение неполадок с Kusto.Explorer](kusto-explorer-troubleshooting.md)

Дополнительные сведения о средствах и служебных программах Kusto. Explorer:
* [Анализатор кода Kusto. Explorer](kusto-explorer-code-analyzer.md)
* [Навигация по коду Kusto. Explorer](kusto-explorer-codenav.md)
* [Рефакторинг кода Kusto. Explorer](kusto-explorer-refactor.md)
* [Язык запросов Kusto (KQL)](https://docs.microsoft.com/azure/kusto/query/)
