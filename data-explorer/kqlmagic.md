---
title: Используйте записную книжку Jupyter для анализа данных в Azure Data Explorer
description: Эта тема показывает, как анализировать данные в Azure Data Explorer с помощью ноутбука Jupyter и расширения Kqlmagic.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33fe6750c355c8dc79bbbd9223166786f844a502
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496957"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Используйте блокнот Jupyter и расширение Kqlmagic для анализа данных в Azure Data Explorer

Jupyter Notebook представляет собой веб-приложение с открытым исходным кодом, которое позволяет создавать документы, содержащие код, формулы, визуализации и текстовое описание, и обмениваться этими документами. Использование включает в себя очистку и преобразование данных, числовое моделирование, статистическое моделирование, визуализацию данных и машинное обучение.
[Jupyter Notebook](https://jupyter.org/) поддерживает магические функции, расширяющие возможности ядра, путем поддержки дополнительных команд. Магическая команда KQL — это команда, которая расширяет возможности ядра Python в Jupyter Notebook, поэтому вы можете выполнять запросы на языке Kusto в собственном коде. Вы можете легко объединить Python и язык запросов Kusto, чтобы запрашивать и визуализировать данные с использованием обширной библиотеки Plot.ly, интегрированной в команды `render`. Поддерживаются источники данных для выполнения запросов. Эти источники данных включают Azure Data Explorer, быструю и высокомасштабируемую службу разведки данных для данных журналов и телеметрии, а также журналы Azure Monitor и Application Insights. Магическая команда KQL также работает с Записными книжками Azure, тестовой службой Jupyter и расширением Jupyter для Visual Studio Code.

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись электронной почты организации, которая входит в Azure Active Directory (AAD).
- Jupyter Notebook установлена на локальном компьютере; вы также можете использовать Записные книжки Azure и клонировать пример [Azure Notebook](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb).

## <a name="install-kql-magic-library"></a>Установка библиотеки магической команды KQL

1. Установка магической команды KQL

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > При использовании Записных книжек Azure этот шаг не является обязательным.

1. Загрузка магической команды KQL

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > Измените версию ядра на Python 3.6, нажав на ядро > изменение ядра > Python 3.6
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Подключитесь к справочному кластеру Azure Data Explorer.

Выполните следующую команду для подключения к базе данных *Samples*, расположенной в кластере *Help*. Если вы пользуетесь не Microsoft AAD, замените имя клиента `Microsoft.com` на имя вашего клиента AAD.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Отправка запросов и визуализация

Запрашивайте данные с помощью [оператора отображения](kusto/query/renderoperator.md) и визуализируйте их с помощью библиотеки ploy.ly. Для запрашивания и визуализации данных используется интегрированный интерфейс, использующий собственный KQL. Kqlmagic поддерживает большинство диаграмм, за исключением `timepivot`, `pivotchart` и `ladderchart`. Отображение поддерживается для всех атрибутов, за исключением `kind`, `ysplit` и `accumulate`. 

### <a name="query-and-render-piechart"></a>Запрос и отображение круговой диаграммы

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Запрос и отображение временной диаграммы

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Эти диаграммы являются интерактивными. Выберите диапазон времени, чтобы изменить масштаб в соответствии с этим диапазоном.

### <a name="customize-the-chart-colors"></a>Настройка цветов диаграммы

Если вам не нравится цветовая палитра по умолчанию, настройте диаграммы, используя варианты палитры. Доступные палитры можно найти здесь: [Выберите цветовую палитру для вашего результата диаграммы магического запроса КЗЛ](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. Для списка палитр:

    ```python
    %kql --palettes -popup_window
    ```

1. Выберите палитру цветов `cool` и снова отобразите результаты запроса.

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Параметризация запроса с помощью Python

Магическая команда KQL позволяет выполнять простой обмен между языком запросов Kusto и Python. Чтобы узнать больше: [Параметризировать ваш магический запрос КЗЛ с Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Использование переменной Python в запросе KQL

Для фильтрации данных можно использовать значение переменной Python в своем запросе.

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>Преобразование результатов запроса в Pandas DataFrame

Вы можете получить доступ к результатам выполнения запроса KQL в Pandas DataFrame. Вы можете получить доступ к результатам последних выполненных запросов с помощью переменной `_kql_raw_result_` и легко преобразовать результаты в Pandas DataFrame следующим образом.

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Пример

Во многих сценариях анализа может потребоваться создать повторно используемые записные книжки, которые содержат множество запросов, и передать результаты из одного запроса в последующие. В следующем примере используется переменная Python `statefilter` для фильтрации данных.

1. Выполнение запроса для просмотра 10 состояний с максимальным значением параметра `DamageProperty`:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Выполнение запроса для получения максимального состояния и запись этого значения в переменную Python:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Выполнение запроса с использованием инструкции `let` и переменной Python:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Выполнение команды help:

    ```python
    %kql --help "help"
    ```

> [!TIP]
> Для получения информации обо `%config Kqlmagic`всех доступных конфигурациях используйте . Для устранения неполадок и захвата ошибок Kusto, таких как проблемы с подключением и неправильные запросы, используйте`%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>Следующие шаги

Выполните команду help, чтобы изучить следующие примеры записных книжек, которые содержат все поддерживаемые функции.
- [Kqlmagic Quick Start](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) (Краткое руководство по Kqlmagic) 
- [Kqlmagic Quick Start](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) (Краткое руководство по Kqlmagic) 
- [Начало работы с магией КЗЛ для журналов Azure Monitor](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Kqlmagic — parametrization features](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) (Функции параметризации Kqlmagic) 
- [Kqlmagic — palette features](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb) (Функции палитры Kqlmagic)
