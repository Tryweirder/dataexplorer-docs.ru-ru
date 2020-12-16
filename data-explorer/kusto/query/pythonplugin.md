---
title: Подключаемый модуль Python — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль Python в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 918d0f2f7fa8667a4cf90b2813bb3b80dd49fa78
ms.sourcegitcommit: 335e05864e18616c10881db4ef232b9cda285d6a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97596861"
---
# <a name="python-plugin"></a>Подключаемый модуль Python

::: zone pivot="azuredataexplorer"

Подключаемый модуль Python запускает определяемую пользователем функцию (UDF) с помощью скрипта Python. Сценарий Python получает в качестве входных данных табличные данные, и предполагается, что он создает табличный вывод.
Среда выполнения подключаемого модуля размещается в [песочницах](../concepts/sandboxes.md), выполняющихся на узлах кластера.

## <a name="syntax"></a>Синтаксис

*T* `|` `evaluate` [ `hint.distribution` `=` ( `single`  |  `per_node` )] `python(` *output_schema* `,` *Скрипт* [ `,` *script_parameters*] [ `,` *external_artifacts*]`)`

## <a name="arguments"></a>Аргументы

* *output_schema*: `type` литерал, определяющий выходную схему табличных данных, возвращаемую кодом Python.
    * Формат: `typeof(` *ColumnName* `:` *ColumnType*[,...] `)` . Например, `typeof(col1:string, col2:long)` .
    * Чтобы расширить входную схему, используйте следующий синтаксис: `typeof(*, col1:string, col2:long)`
* *Скрипт*: `string` литерал, который является допустимым сценарием Python для выполнения.
* *script_parameters*: Необязательный `dynamic` литерал. Это контейнер свойств пар "имя-значение", которые передаются в скрипт Python в качестве зарезервированного `kargs` словаря. Дополнительные сведения см. в разделе [зарезервированные переменные Python](#reserved-python-variables).
* *Указание. распространение*: необязательное указание на выполнение подключаемого модуля, распределенное по нескольким узлам кластера.
  * Значение по умолчанию — `single`.
  * `single`: Один экземпляр скрипта будет выполняться по всем данным запроса.
  * `per_node`: Если запрос перед распределением блока Python выполняется, экземпляр скрипта будет выполняться на каждом узле на содержащихся в нем данных.
* *external_artifacts*: Необязательный `dynamic` литерал, который является контейнером свойств пар имен и URL-адресов для артефактов, доступных из облачного хранилища. Их можно сделать доступными для использования сценарием во время выполнения.
  * URL-адреса, указанные в этом контейнере свойств, должны быть:
    * Включается в [политику выноски](../management/calloutpolicy.md)кластера.
    * В общедоступном расположении или предоставьте необходимые учетные данные, как описано в [строках подключения к хранилищу](../api/connection-strings/storage.md).
  * Артефакты становятся доступными для использования сценарием из локального временного каталога `.\Temp` . Имена, указанные в контейнере свойств, используются в качестве имен локальных файлов. См. [примеры](#examples).
  * Дополнительные сведения см. [в разделе Установка пакетов для подключаемого модуля Python](#install-packages-for-the-python-plugin). 

## <a name="reserved-python-variables"></a>Зарезервированные переменные Python

Следующие переменные зарезервированы для взаимодействия между языком запросов Kusto и кодом Python.

* `df`: Входные табличные данные (значения `T` выше) в виде кадров данных `pandas` .
* `kargs`: Значение аргумента *script_parameters* в виде словаря Python.
* `result`: `pandas` Кадр данных, созданный скриптом Python, значение которого преобразуется в табличные данные, отправляемые оператору запроса Kusto, который следует за подключаемым модулем.

## <a name="enable-the-plugin"></a>Включение подключаемого модуля

* По умолчанию подключаемый модуль отключен.
* Сведения о включении подключаемого модуля см. в списке [предварительных требований](../concepts/sandboxes.md#prerequisites).
* Включите или отключите подключаемый модуль в портал Azure на [вкладке конфигурации](../../language-extensions.md)кластера.

## <a name="python-sandbox-image"></a>Образ песочницы Python

* Образ "песочницы Python" основан на *Anaconda 5.2.0* Distribution с ядром *Python 3,6* .
  См. список [пакетов Anaconda](http://docs.anaconda.com/anaconda/packages/old-pkg-lists/5.2.0/py3.6_win-64/).
  
  > [!NOTE]
  > Небольшой процент пакетов может быть несовместим с ограничениями, принудительно выполняемыми песочницей, в которой выполняется подключаемый модуль.
  
* Образ Python также содержит распространенные пакеты машинного обучения: `tensorflow` , `keras` , `torch` ,, `hdbscan` `xgboost` и другие полезные пакеты.
* Подключаемый модуль импортирует *NumPy* (как `np` ) & *Pandas* (AS) по `pd` умолчанию.  При необходимости можно импортировать другие модули.

## <a name="use-ingestion-from-query-and-update-policy"></a>Использование приема запросов и политик обновления

* Используйте подключаемый модуль в запросах, которые:
  * Определяется как часть [политики обновления](../management/updatepolicy.md), исходная таблица которой принимается для использования приема *без потоковой передачи* .
  * Выполните команду как часть команды, [принимающей запрос](../management/data-ingestion/ingest-from-query.md), например `.set-or-append` .
    В обоих случаях убедитесь, что объем и частота приема, а также сложность и ресурсы, используемые логикой Python, выровняйте с [ограничениями песочницы](../concepts/sandboxes.md#limitations) и доступными ресурсами кластера. Несоблюдение этого действия может привести к [ошибкам регулирования](../concepts/sandboxes.md#errors).
* Нельзя использовать подключаемый модуль в запросе, который определен как часть политики обновления, исходная таблица которого принимается с помощью [приема потоковой передачи](../../ingest-data-streaming.md).

## <a name="examples"></a>Примеры

```kusto
range x from 1 to 360 step 1
| evaluate python(
//
typeof(*, fx:double),               //  Output schema: append a new fx column to original table 
//
'result = df\n'                     //  The Python decorated script
'n = df.shape[0]\n'
'g = kargs["gain"]\n'
'f = kargs["cycles"]\n'
'result["fx"] = g * np.sin(df["x"]/n*2*np.pi*f)\n'
//
, pack('gain', 100, 'cycles', 4)    //  dictionary of parameters
)
| render linechart 
```

:::image type="content" source="images/plugin/sine-demo.png" alt-text="синусная демонстрация" border="false":::

```kusto
print "This is an example for using 'external_artifacts'"
| evaluate python(
    typeof(File:string, Size:string),
    "import os\n"
    "result = pd.DataFrame(columns=['File','Size'])\n"
    "sizes = []\n"
    "path = '.\\\\Temp'\n"
    "files = os.listdir(path)\n"
    "result['File']=files\n"
    "for file in files:\n"
    "    sizes.append(os.path.getsize(path + '\\\\' + file))\n"
    "result['Size'] = sizes\n"
    "\n",
    external_artifacts = 
        dynamic({"this_is_my_first_file":"https://kustoscriptsamples.blob.core.windows.net/samples/R/sample_script.r",
                 "this_is_a_script":"https://kustoscriptsamples.blob.core.windows.net/samples/python/sample_script.py"})
)
```

| Файл                  | Размер |
|-----------------------|------|
| this_is_a_script      | 120  |
| this_is_my_first_file | 105  |

## <a name="performance-tips"></a>Советы по улучшению производительности

* Сократите набор входных данных подключаемого модуля до минимально необходимого объема (столбцов или строк).
    * Используйте фильтры в исходном наборе данных, если это возможно, с языком запросов Kusto.
    * Чтобы выполнить вычисление для подмножества исходных столбцов, перед вызовом подключаемого модуля необходимо проецировать только эти столбцы.
* Используется `hint.distribution = per_node` каждый раз, когда логика в скрипте является распространяемой.
    * Можно также использовать [оператор Partition](partitionoperator.md) для секционирования входного набора данных.
* Используйте язык запросов Kusto, когда это возможно, чтобы реализовать логику скрипта Python.

    ### <a name="example"></a>Пример

    ```kusto    
    .show operations
    | where StartedOn > ago(7d) // Filtering out irrelevant records before invoking the plugin
    | project d_seconds = Duration / 1s // Projecting only a subset of the necessary columns
    | evaluate hint.distribution = per_node python( // Using per_node distribution, as the script's logic allows it
        typeof(*, _2d:double),
        'result = df\n'
        'result["_2d"] = 2 * df["d_seconds"]\n' // Negative example: this logic should have been written using Kusto's query language
      )
    | summarize avg = avg(_2d)
    ```

## <a name="usage-tips"></a>Советы по использованию

* Чтобы создать многострочные строки, содержащие скрипт Python в `Kusto.Explorer` , скопируйте скрипт Python из любимого редактора Python (*Jupyter*, *Visual Studio Code*, *PyCharm* и т. д.). 
  Теперь выполните одно из следующих действий.
    * Нажмите клавишу **F2** , чтобы открыть окно *изменить в Python* . Вставьте сценарий в это окно. Щелкните **ОК**. Сценарий будет дополнен кавычками и новыми строками, поэтому он действителен в Kusto и автоматически вставляется на вкладку запрос.
    * Вставьте код Python непосредственно на вкладку запрос. Выберите эти строки и нажмите клавиши **CTRL + K**, **CTRL + S** , чтобы снабдить их, как показано выше. Чтобы обратить, нажмите клавиши **CTRL + K**, **CTRL + M** горячие клавиши. См. полный список [ярлыков редактора запросов](../tools/kusto-explorer-shortcuts.md#query-editor).
* Чтобы избежать конфликтов между разделителями строк Kusto и строковыми литералами Python, используйте:
     * Символы одинарной кавычки ( `'` ) для строковых литералов Kusto в запросах Kusto
     * Символы двойной кавычки ( `"` ) для строковых литералов Python в скриптах Python
* Используйте [ `externaldata` оператор](externaldata-operator.md) , чтобы получить содержимое скрипта, сохраненного во внешнем расположении, например в хранилище BLOB-объектов Azure.
  
    ### <a name="example"></a>Пример

    ```kusto
    let script = 
        externaldata(script:string)
        [h'https://kustoscriptsamples.blob.core.windows.net/samples/python/sample_script.py']
        with(format = raw);
    range x from 1 to 360 step 1
    | evaluate python(
        typeof(*, fx:double),
        toscalar(script), 
        pack('gain', 100, 'cycles', 4))
    | render linechart 
    ```

## <a name="install-packages-for-the-python-plugin"></a>Установка пакетов для подключаемого модуля Python

Вам может потребоваться установить пакеты самостоятельно по следующим причинам:

* Пакет является частным и собственным.
* Пакет является общедоступным, но не включен в базовый образ подключаемого модуля.

Установите пакеты следующим образом.

### <a name="prerequisites"></a>Предварительные требования

  1. Создайте контейнер больших двоичных объектов для размещения пакетов, предпочтительно в том же месте, что и кластер. Например, `https://artifcatswestus.blob.core.windows.net/python` Если кластер находится в западной части США.
  1. Измените [политику выноски](../management/calloutpolicy.md) кластера, чтобы разрешить доступ к этому расположению.
        * Для этого изменения требуются разрешения [аллдатабасесадмин](../management/access-control/role-based-authorization.md) .

        * Например, чтобы разрешить доступ к большому двоичному объекту, расположенному в `https://artifcatswestus.blob.core.windows.net/python` , выполните следующую команду:

        ```kusto
        .alter-merge cluster policy callout @'[ { "CalloutType": "sandbox_artifacts", "CalloutUriRegex": "artifcatswestus\\.blob\\.core\\.windows\\.net/python/","CanCall": true } ]'
        ```

### <a name="install-packages"></a>Установка пакетов

1. Для общедоступных пакетов в [PyPi](https://pypi.org/) или других каналах Скачайте пакет и его зависимости.

   * Компилировать файлы колеса ( `*.whl` ), если это необходимо.
   * В окне cmd в локальной среде Python выполните:
    
    ```python
    pip wheel [-w download-dir] package-name.
    ```

1. Создайте ZIP-файл, содержащий требуемый пакет и его зависимости.

    * Для частных пакетов: ZIP-папка пакета и папки его зависимостей.
    * Для общедоступных пакетов ZIP-файлы, скачанные на предыдущем шаге.
    
    > [!NOTE]
    > * Обязательно запишите `.whl` сами файлы, а не их родительскую папку.
    > * Вы можете пропустить `.whl` файлы для пакетов, которые уже существуют с той же версией в базовом образе песочницы.

1. Отправьте сжатый ZIP-файл в большой двоичный объект в расположении артефактов (из шага 1).

1. Вызовите `python` подключаемый модуль.
    * Укажите `external_artifacts` параметр с контейнером свойств Name и ссылкой на ZIP-файл (URL для большого двоичного объекта).
    * В встроенном коде Python импортируйте `Zipackage` из `sandbox_utils` и вызовите его `install()` метод с именем ZIP-файла.

### <a name="example"></a>Пример

Установите пакет подпрограммы [имитации](https://pypi.org/project/Faker/) , который создает фиктивные данные.

```kusto
range ID from 1 to 3 step 1 
| extend Name=''
| evaluate python(typeof(*),
    'from sandbox_utils import Zipackage\n'
    'Zipackage.install("Faker.zip")\n'
    'from faker import Faker\n'
    'fake = Faker()\n'
    'result = df\n'
    'for i in range(df.shape[0]):\n'
    '    result.loc[i, "Name"] = fake.name()\n',
    external_artifacts=pack('faker.zip', 'https://artifacts.blob.core.windows.net/kusto/Faker.zip?...'))
```

| ID | Имя         |
|----|--------------|
|   1| Гари Тапиа   |
|   2| Емма Эванс   |
|   3| Ashley БОВЕН |

---

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
