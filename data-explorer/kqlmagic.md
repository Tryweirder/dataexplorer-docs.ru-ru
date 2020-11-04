---
title: Использование Jupyter Notebook для анализа данных в Azure обозреватель данных
description: В этом разделе показано, как анализировать данные в Azure обозреватель данных с помощью Jupyter Notebook и расширения кклмагик.
author: orspod
ms.author: orspodek
ms.reviewer: maraheja
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/20/2020
ms.openlocfilehash: 857c654c70a2170a42902514718a52fbf7b02944
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349466"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Использование расширения Jupyter Notebook и кклмагик для анализа данных в Azure обозреватель данных

Jupyter Notebook представляет собой веб-приложение с открытым исходным кодом, которое позволяет создавать документы, содержащие код, формулы, визуализации и текстовое описание, и обмениваться этими документами. Использование включает в себя очистку и преобразование данных, числовое моделирование, статистическое моделирование, визуализацию данных и машинное обучение.
[Jupyter Notebook](https://jupyter.org/) поддерживает магические функции, расширяющие возможности ядра, путем поддержки дополнительных команд. кклмагик — это команда, которая расширяет возможности ядра Python в Jupyter Notebook, что позволяет выполнять запросы языка Kusto изначально. Вы можете легко сочетать язык запросов Python и Kusto для запроса и визуализации данных с помощью расширенной библиотеки Plot.ly, интегрированной с `render` командами. Поддерживаются источники данных для выполнения запросов. Эти источники данных включают Azure обозреватель данных, быструю и масштабируемую службу просмотра данных для данных журналов и телеметрии, а также Azure Monitor журналы и Application Insights. кклмагик также работает с расширениями Azure Data Studio, Jupyter Lab и Visual Studio Code Jupyter.

## <a name="prerequisites"></a>Обязательные условия

- Учетная запись электронной почты организации, которая является членом Azure Active Directory (Azure AD).
- Jupyter Notebook, установленные на локальном компьютере, или используйте [Azure Data Studio](/sql/azure-data-studio/notebooks/notebooks-kqlmagic?view=sql-server-ver15)

## <a name="install-kqlmagic-library"></a>Установка библиотеки кклмагик

1. Установка кклмагик:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```

1. Загрузить кклмагик:

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > Измените версию ядра на Python 3,6, щелкнув ядро > изменить ядро > Python 3,6
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Подключитесь к справочному кластеру Azure Data Explorer.

Выполните следующую команду для подключения к базе данных *Samples* , расположенной в кластере *Help*. Для пользователей, не являющихся Microsoft Azure AD, замените имя клиента `Microsoft.com` своим клиентом Azure AD.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

> [!Note]
> Если вы используете собственный кластер ADX, включите в строку подключения регион следующим образом:   
   ```%kql azuredataexplorer://tenant="youecompany.com";code;cluster='mycluster.westus';database='mykustodb'```

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

Если вы не хотите использовать палитру цветов по умолчанию, настройте диаграммы с помощью параметров палитры. Доступные палитры можно найти здесь: [Выбор палитры цветов для результатов в диаграмме запроса кклмагик](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

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

Kqlmagic позволяет выполнять простое взаимодействие между языком запросов Kusto и Python. Дополнительные сведения: [параметризация запроса кклмагик с помощью Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

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
> Для получения сведений обо всех доступных конфигурациях используйте `%config Kqlmagic` . Для устранения неполадок и записи ошибок Kusto, таких как проблемы подключения и неправильные запросы, используйте `%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>Дальнейшие действия

Выполните команду help, чтобы изучить следующие примеры записных книжек, которые содержат все поддерживаемые функции.
- [Начало работы с кклмагик для Azure обозреватель данных](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Начало работы с кклмагик для Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Приступая к работе с кклмагик для журналов Azure Monitor](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Параметризе свой запрос кклмагик с помощью Python](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Выбор палитры цветов для результата кклмагик запроса диаграммы](https://mybinder.org/v2/gh/Microsoft/jupyter-kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)