---
title: Краткое руководство. Запрос данных в пользовательском веб-интерфейсе Azure Data Explorer
description: В этом кратком руководстве вы узнаете, как запрашивать данные из пользовательского веб-интерфейса Azure Data Explorer и предоставлять к ним общий доступ.
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: quickstart
ms.date: 02/09/2021
ms.localizationpriority: high
ms.openlocfilehash: d581ade4a9cbba083e8cebf39a30f01586d2635c
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100360204"
---
# <a name="quickstart-query-data-in-azure-data-explorer-web-ui"></a>Краткое руководство. Запрос данных в пользовательском веб-интерфейсе Azure Data Explorer

Azure Data Explorer — это быстрая и полностью управляемая служба для анализа больших объемов потоковых данных в реальном времени. Azure Data Explorer предоставляет веб-интерфейс, который позволяет подключаться к кластерам Azure Data Explorer, а также создавать, запускать и совместно использовать команды и запросы языка запросов Kusto. С веб-интерфейсом можно работать на портале Azure и как с отдельным веб-приложением, [пользовательским веб-интерфейсом Azure Data Explorer](https://dataexplorer.azure.com). Кроме того, пользовательский веб-интерфейс Azure Data Explorer может размещаться на других веб-порталах на основе HTML. Дополнительные сведения о размещении веб-интерфейса и используемого редактора Monaco см. в руководстве по [интеграции IDE](kusto/api/monaco/monaco-kusto.md).
В этом кратком руководстве показано, как использовать отдельный пользовательский веб-интерфейс Azure Data Explorer.

:::image type="content" source="media/web-query-data/walkthrough.gif" alt-text="Пошаговое руководство по использованию веб-обозревателя Kusto":::

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начать работу.
* Кластер и база данных с данными. [Создайте собственный кластер](create-cluster-database-portal.md) или используйте вспомогательный кластер Azure Data Explorer.

## <a name="sign-in-to-the-application"></a>Вход в приложение

Выполните [вход в приложение](https://dataexplorer.azure.com/).

## <a name="add-clusters"></a>Добавление кластеров

При первом открытии приложения кластер не подключен.

![Добавление кластера](media/web-query-data/add-cluster.png)

Перед выполнением запросов необходимо добавить подключение к кластеру. В этом разделе показано, как добавить подключения к **вспомогательному** кластеру Azure Data Explorer и протестировать кластер, созданный при работе с разделом [Предварительные требования](#prerequisites).

### <a name="add-help-cluster"></a>Добавление вспомогательного кластера

1. В верхнем углу приложения слева выберите элемент **Добавить кластер**.

1. В диалоговом окне **Добавление кластера** введите универсальный код ресурса https://help.kusto.windows.net и щелкните команду **Добавить**.
   
1. В левой области вы увидите **справочный** кластер. Разверните базу данных **Примеры** и откройте папку **Таблицы**, чтобы просмотреть примеры таблиц, к которым у вас есть доступ.

    :::image type="content" source="media/web-query-data/help-cluster.png" alt-text="Поиск таблицы во вспомогательном кластере":::

Впоследствии в этом кратком руководстве и в других статьях по обозревателю данных Azure используется таблица **StormEvents**. 

### <a name="add-your-cluster"></a>Добавление кластера

Теперь добавьте созданный тестовый кластер.

1. Выберите элемент **Добавить кластер**.

1. В диалоговом окне **Добавление кластера** введите URL-адрес тестового кластера в форму `https://<ClusterName>.<Region>.kusto.windows.net/` и щелкните команду **Добавить**. Например `https://mydataexplorercluster.westus.kusto.windows.net`, как показано на следующем изображении:

    :::image type="content" source="media/web-query-data/server-uri.png" alt-text="Ввод URL-адреса тестового кластера":::
    
1. В приведенном ниже примере вы видите **справочный** кластер и новый кластер **docscluster.westus** (полный URL-адрес — `https://docscluster.westus.kusto.windows.net/`).

    ![Тестовый кластер](media/web-query-data/test-cluster.png)

## <a name="run-queries"></a>Выполнение запросов

Теперь можно выполнять запросы к обоим кластерам (если в тестовом кластере есть данные). В этой статье основное внимание уделяется работе со **вспомогательным** кластером.

1. В левой области в разделе **справочный** кластер выберите базу данных **Примеры**.

1. Копируйте и вставьте в окно запроса следующий запрос. Вверху окна выберите **Выполнить**.

    ```kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    
    Этот запрос возвращает десять последних записей в таблице **StormEvents**. В результате должно отобразиться что-то вроде следующей таблицы.

    :::image type="content" source="media/web-query-data/result-set-take-10.png" alt-text="Снимок экрана: таблица, которая содержит данные для десяти событий непогоды" border="false":::

    На приведенном ниже рисунке показано состояние приложения с добавленным кластером и запросом с результатами.

    :::image type="content" source="media/web-query-data/webui-take10.png" alt-text="Полноэкранный режим":::

1. Копируйте и вставьте в окно запроса под первым запросом следующий запрос. Обратите внимание, что он не отформатирован и весь текст находится в одной строке.

    ```kusto
    StormEvents | sort by StartTime desc 
    | project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative | take 10
    ```

1. Выберите новый запрос. Нажмите клавиши *SHIFT+ALT+F*, чтобы отформатировать запрос, который должен выглядеть, как показано ниже.

    ![Форматированный запрос](media/web-query-data/formatted-query.png)

1. Щелкните элемент **Выполнить** или нажмите клавиши *SHIFT+ВВОД*, чтобы выполнить запрос. Этот запрос возвращает те же записи, что и первый, но включает только столбцы, указанные в инструкции `project`. В результате должно отобразиться что-то вроде следующей таблицы.

    :::image type="content" source="media/web-query-data/result-set-project.png" alt-text="Снимок экрана: таблица с данными о времени начала, времени завершения, состоянии, типе события, материальном ущербе и описанием эпизода для десяти событий непогоды" border="false":::

    > [!TIP]
    > Выберите элемент **Отозвать** в верхней части окна запроса, чтобы отобразить результирующий набор из первого запроса без повторного выполнения запроса. Часто во время анализа выполняется несколько запросов. Элемент управления **Отозвать** позволяет получить результаты предыдущих запросов.

1. Давайте выполним еще один запрос, чтобы увидеть другой тип выходных данных.

    ```kusto
    StormEvents
    | summarize event_count=count(), mid = avg(BeginLat) by State
    | sort by mid
    | where event_count > 1800
    | project State, event_count
    | render columnchart
    ```

    Результат должен напоминать следующую схему.

    ![Гистограмма](media/web-query-data/column-chart.png)

    > [!NOTE]
    > Пустые строки в выражении запроса могут влиять на то, какая часть запроса выполняется.
    > * Если текст не выбран, предполагается, что запрос или команда разделены пустыми строками.
    > * Если текст выбран, будет выполнен выделенный текст.

## <a name="work-with-the-table-grid"></a>Работа с сеткой таблицы

Теперь, когда вы знаете, как работают базовые запросы, вы можете использовать сетку таблицы для настройки результатов и дальнейшего анализа. 

### <a name="expand-a-cell"></a>Развертывание ячейки

Развертывание ячеек удобно для просмотра длинных строк или динамических полей, например JSON. 

1. Чтобы открыть развернутое представление, дважды щелкните ячейку. Это представление позволяет считывать длинные строки и обеспечивает форматирование JSON для динамических данных.

    :::image type="content" source="media/web-query-data/expand-cell.png" alt-text="Развертывание ячейки в веб-интерфейсе Azure Data Explorer для отображения длинных строк":::

1. Чтобы переключить режим области чтения, щелкните значок справа в верхнем углу сетки результатов. Выберите один из следующих режимов области чтения для развернутого представления: встроенный, область внизу или область справа.

    :::image type="content" source="media/web-query-data/expanded-view-icon.png" alt-text="Значок выбора области чтения для режима развернутого представления — результаты запроса в веб-интерфейсе Azure Data Explorer":::

### <a name="expand-a-row"></a>Развертывание строки

При работе с таблицей с десятками столбцов вы можете развернуть всю строку, чтобы легко просмотреть общие сведения о различных столбцах и их содержимом. 

1. Щелкните стрелку **>** слева от строки, которую нужно развернуть.

    :::image type="content" source="media/web-query-data/expand-row.png" alt-text="Развертывание строки в веб-интерфейсе Azure Data Explorer":::

1. В развернутой строке некоторые столбцы развернуты (стрелка указывает вниз), а некоторые — свернуты (стрелка указывает вправо). Чтобы переключиться с одного режима на второй, щелкните соответствующую стрелку.

### <a name="group-column-by-results"></a>Группировка столбцов по результатам

В результатах можно выполнять группирование по любому столбцу.

1. Выполните приведенный ниже запрос:
     
    ```kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```

1. Наведите указатель мыши на столбец **Штат**, выберите меню и пункт **Группировать по штату**.

    ![Группировка по штату](media/web-query-data/group-by.png)

1. В сетке щелкните элемент **Калифорния**, чтобы просмотреть записи для этого штата. Этот тип группировки полезен в исследовательском анализе.

    :::image type="content" source="media/web-query-data/group-expanded.png" alt-text="Снимок экрана: сетка результатов запросов с развернутыми данными для штата Калифорния" border="false":::

1. Наведите указатель мыши на столбец **Группировка** и выберите **Сбросить столбцы**. Этот параметр позволяет вернуть сетку в исходное состояние.

    ![Сброс столбцов](media/web-query-data/reset-columns.png)

#### <a name="use-value-aggregation"></a>Использование агрегирования значений

После группировки по столбцу можно выполнить простые статистические вычисления для каждой группы с помощью функции агрегирования значений.

1. Откройте меню для столбца, который необходимо вычислить.
1. Выберите элемент **Агрегирование значений**, а затем выберите тип функции, которую нужно выполнить в этом столбце.

    :::image type="content" source="media/web-query-data/aggregate.png" alt-text="Агрегирование результатов при группировании столбцов по результатам.":::

### <a name="filter-columns"></a>Фильтрация столбцов

Для фильтрации результатов в столбце можно использовать один или несколько операторов.

1. Чтобы отфильтровать тот или иной столбец, выберите меню для этого столбца.
1. Щелкните значок фильтра.
1. Выберите нужный оператор в построителе фильтров.
1. Введите выражение, по которому нужно отфильтровать столбец. Результаты будут фильтроваться по мере ввода.
    
    > [!NOTE] 
    > Регистр в фильтре не учитывается.

1. Чтобы создать фильтр с несколькими условиями, выберите логический оператор для добавления еще одного условия.
1. Чтобы удалить фильтр, удалите текст из первого условия фильтра.

    :::image type="content" source="media/web-query-data/filter-column.gif" alt-text="Изображение GIF, на котором показано, как выполнить фильтрацию по столбцу в веб-интерфейсе Azure Data Explorer":::

### <a name="run-cell-statistics"></a>Статистические вычисления по ячейкам

1. Выполните следующий запрос.

    ```kusto
    StormEvents
    | sort by StartTime desc
    | where DamageProperty > 5000
    | project StartTime, State, EventType, DamageProperty, Source
    | take 10
    ```

1. В сетке результатов выберите несколько числовых ячеек. Сетка таблицы позволяет выбрать несколько строк, столбцов и ячеек и вычислить для них агрегированные значения. Сейчас веб-интерфейс поддерживает следующие функции для числовых значений: **Average**, **Count**, **Min**, **Max** и **Sum**.

    :::image type="content" source="media/web-query-data/select-stats.png" alt-text="Выбор функций"::: 

### <a name="filter-to-query-from-grid"></a>Фильтрация для запроса из сетки

Еще один простой способ отфильтровать сетку — добавить в запрос оператор фильтра непосредственно из сетки.

1. Выберите ячейку с содержимым, для которого нужно создать фильтр запроса.
1. Щелкните правой кнопкой мыши, чтобы открыть для ячейки меню действий. Выберите **Добавить выделение как фильтры**.
    
    :::image type="content" source="media/web-query-data/add-selection-filter.png" alt-text="Добавление выделенного фрагмента в качестве фильтра для запроса из сетки результаты в веб-интерфейсе Azure Data Explorer":::

1. В редакторе запросов в запрос будет добавлено предложение запроса:

    :::image type="content" source="media/web-query-data/add-query-from-filter.png" alt-text="Добавление предложения запроса из фильтрации в сетке в веб-интерфейсе Azure Data Explorer":::

### <a name="pivot"></a>Сведение

Функция сводного режима аналогична сводной таблице Excel, что позволяет выполнять расширенный анализ в самой сетке.

Сведение позволяет брать значения из столбцов и преобразовывать их в столбцы. Например, можно выполнить сведение по *штату*, чтобы создать столбцы для Флориды, Миссури, Алабамы и т. д.

1. В правой части сетки выберите элемент **Столбцы**, чтобы просмотреть панель инструментов таблицы.

    ![Панель инструментов таблицы](media/web-query-data/tool-panel.png)

1. Выберите **Сводный режим** и перетащите столбцы следующим образом: **EventType** в **Группы строк**, **DamageProperty** в **Значения**, а **State** — в **Метки столбцов**.  

    ![Сводный режим](media/web-query-data/pivot-mode.png)

    Результаты должны выглядеть, как в следующей сводной таблице:

    ![Сводная таблица](media/web-query-data/pivot-table.png)

## <a name="search-in-the-results-table"></a>Поиск в таблице результатов

Вы можете найти определенное выражение в таблице результатов.

1. Выполните приведенный ниже запрос:

    ```Kusto
    StormEvents
    | where DamageProperty > 5000
    | take 1000
    ```

1. Нажмите кнопку **Поиск** справа и введите *Wabash*.

    :::image type="content" source="media/web-query-data/search.png" alt-text="Поиск в таблице":::

1. Все упоминания требуемого выражения теперь выделены в таблице. Теперь нажимайте клавишу *ВВОД*, чтобы перейти вперед, или клавиши *SHIFT+ENTER*, чтобы вернуться назад. Кроме того, можно использовать кнопки со стрелками *вверх* и *вниз* рядом с полем поиска.

    :::image type="content" source="media/web-query-data/search-results.png" alt-text="Навигация по результатам поиска":::

## <a name="share-queries"></a>Обмен запросами

Очень часто требуется предоставить общий доступ к создаваемым запросам. 

1. В окне запроса выберите первый скопированный вами запрос.

1. В верхней части окна запроса выберите **Поделиться**. 

    :::image type="content" source="media/web-query-data/share-menu.png" alt-text="Меню Общий доступ":::

В раскрывающемся списке содержатся следующие параметры:
* Link to clipboard (Копировать ссылку);
* [Link query to clipboard](#provide-a-deep-link) (Копировать ссылку и запрос);
* Link, query, results to clipboard (Копировать ссылку, запрос и результаты);
* [Закрепить на панели мониторинга](#pin-to-dashboard)
* [Query to Power BI](power-bi-imported-query.md) (Запрос в Power BI).

### <a name="provide-a-deep-link"></a>Предоставление прямой ссылки

Можно предоставить глубокую ссылку, чтобы другие пользователи с доступом в кластер могли выполнять запросы.

1. В меню **Общий доступ** выберите элемент **Link, query to clipboard** (Копировать ссылку и запрос в буфер обмена).

1. Копируйте ссылку и запрос в текстовый файл.

1. Вставьте ссылку в новом окне браузера. Результат должен выглядеть примерно так:

    :::image type="content" source="media/web-query-data/shared-query.png" alt-text="Прямая ссылка на общий запрос":::

### <a name="pin-to-dashboard"></a>Закрепить на панели мониторинга

Когда вы выполняете анализ данных с помощью запросов в веб-интерфейсе и находите нужные данные, можно закрепить их на панели мониторинга для непрерывного мониторинга. 

Закрепление запроса:

1. В меню **Общий доступ** щелкните элемент **Закрепить на панели мониторинга**.

1. В области **Закрепить на панели мониторинга**:
    1. Укажите **имя запроса**.
    1. Выберите вариант **Использовать существующую** или **Создать новую**.
    1. Укажите **имя панели мониторинга**.
    1. Установите флажок **View dashboard after creation** (Просмотреть панель мониторинга после создания), если это новая панель мониторинга.
    1. Выберите **Закрепить**.

    :::image type="content" source="media/web-query-data/pin-to-dashboard.png" alt-text="Закрепление на панели мониторинга":::

> [!NOTE]
> На **панели мониторинга закрепляется** только выбранный запрос. Чтобы создать источник данных панели мониторинга и преобразовать команды отрисовки в визуальный элемент на панели мониторинга, необходимо выбрать соответствующую базу данных в списке.

## <a name="export-query-results"></a>Экспорт результатов запроса

Чтобы экспортировать результаты запроса в CSV-файл, последовательно выберите элементы **Файл** > **Экспортировать в CSV-файл**.

:::image type="content" source="media/web-query-data/export-results.png" alt-text="Экспорт результатов в CSV-файл":::

## <a name="settings"></a>Параметры

На вкладке **Параметры** можно:

* [экспортировать параметры среды](#export-environment-settings);
* [импортировать параметры среды](#import-environment-settings);
* [выделить уровни ошибок;](#highlight-error-levels)
* [очищать локальное состояние](#clean-up-resources).

Щелкните значок параметров :::image type="icon" source="media/web-query-data/settings-icon.png" border="false"::: вверху справа, чтобы открыть окно **Параметры**.

:::image type="content" source="media/web-query-data/settings.png" alt-text="Окно &quot;Параметры&quot;":::

### <a name="export-and-import-environment-settings"></a>Экспорт и импорт параметров среды

Действия экспорта и импорта помогают защитить рабочую среду и перемещать ее в другие браузеры и устройства. Действие экспорта позволяет экспортировать все параметры, подключения к кластеру и вкладки запросов в JSON-файл, который можно импортировать в другой браузер или на другое устройство.

#### <a name="export-environment-settings"></a>Экспорт параметров среды

1. В окне **Параметры** > **Общие** щелкните элемент **Экспорт**.
1. Файл **adx-export.json** будет скачан в локальное хранилище.
1. Выберите команду **Очистить локальное состояние**, чтобы вернуть среду в исходное состояние. Этот параметр позволяет удалить все подключения к кластеру и закрыть открытые вкладки.

> [!NOTE]
> **Экспорт** охватывает только данные, связанные с запросом. Данные панели мониторинга не будут экспортированы в файл **adx-export.json**.

#### <a name="import-environment-settings"></a>Импорт параметров среды

1. В окне **Параметры** > **Общие** щелкните элемент **Импорт**. Затем во всплывающем окне **Предупреждение** выберите элемент **Импорт**.

    :::image type="content" source="media/web-query-data/import.png" alt-text="Предупреждение, связанное с импортом":::

1. Перейдите к файлу **adx-export.json** из локального хранилища и откройте его.
1. Станут доступными предыдущие подключения к кластеру и открытые вкладки.

> [!NOTE]
> **Импорт** переопределяет все существующие параметры среды и данные.

### <a name="highlight-error-levels"></a>Выделение уровней ошибок

Kusto пытается интерпретировать уровень серьезности или детализации каждой строки на панели результатов и соответствующим образом выделить их цветом. Для этого сопоставляются различные значения каждого столбца с набором известных шаблонов ("предупреждение", "ошибка" и т. д.). 

#### <a name="enable-error-level-highlighting"></a>Включение выделения уровня ошибки

Чтобы включить выделение уровня ошибки, сделайте следующее:

1. Щелкните значок **Параметры** рядом с именем пользователя.
1. Выберите вкладку **Внешний вид** и перетяните переключатель **Включить выделение уровня ошибки** вправо. 

    :::image type="content" source="media/web-query-data/enable-error-highlighting.gif" alt-text="GIF с анимацией: включение выделения уровня ошибки в параметрах":::

Цветовая схема для уровней ошибок в **светлом** режиме | Цветовая схема для уровней ошибок в **темном** режиме
|---|---|
:::image type="content" source="media/web-query-data/light-mode.png" alt-text="Снимок экрана: условные обозначения для цветовой схемы в светлом режиме"::: | :::image type="content" source="media/web-query-data/dark-mode.png" alt-text="Снимок экрана: условные обозначения для цветовой схемы в темном режиме":::

#### <a name="column-requirements-for-highlighting"></a>Требования к столбцам для выделения

Чтобы уровни ошибок выделялись, тип столбца должен быть int, long или string. 

* Для столбца типа `long` или `int`:
   * необходимо использовать имя столбца *Level*;
   * допустимы значения от 1 до 5.
* Для столбца типа `string`: 
   * для повышения производительности при необходимости можно использовать имя столбца *Level*; 
   * столбец может содержать только одно из следующих значений:
       * critical, crit, fatal, assert, high;
       * error, e;
       * warning, w, monitor;
       * сведения
       * verbose, verb, d.
   
## <a name="provide-feedback"></a>Отзывы

1. В верхнем углу окна приложения справа щелкните значок обратной связи :::image type="icon" source="media/web-query-data/icon-feedback.png" border="false":::.

1. Введите свой отзыв, а затем выберите **Отправить**.

## <a name="clean-up-resources"></a>Очистка ресурсов

В этом кратком руководстве не было создано никаких ресурсов, однако если вы хотите удалить один или оба кластера из приложения, щелкните кластер правой кнопкой и выберите **Удалить подключение**.
Кроме того, можно выбрать элемент **Очистить локальное состояние** на вкладке **Параметры** > **Общие**. После этого действия будут удалены все подключения к кластеру и закрыты все открытые вкладки запросов.

## <a name="next-steps"></a>Дальнейшие действия

[Написание запросов для обозревателя данных Azure](write-queries.md)
