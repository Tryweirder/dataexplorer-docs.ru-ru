---
title: Подключаемый модуль Python в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается подключаемый модуль Python в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5ceafde1361c87d368237d0f8c71ad8d0708aec1
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108513"
---
# <a name="python-plugin"></a>Подключаемый модуль Python

::: zone pivot="azuredataexplorer"

Подключаемый модуль Python запускает определяемую пользователем функцию (UDF) с помощью скрипта Python. Сценарий Python получает в качестве входных данных табличные данные, и предполагается, что он создает табличный вывод.
Среда выполнения подключаемого модуля размещается в [песочницах](../concepts/sandboxes.md), выполняющихся на узлах кластера.

## <a name="syntax"></a>Синтаксис

*T* `|` `single``,` *script* *output_schema* `,` *external_artifacts*[`hint.distribution` (`per_node`)] `python(`output_schema скрипт [`,` *script_parameters*] [external_artifacts] |  `evaluate` `=``)`

## <a name="arguments"></a>Аргументы

* *output_schema*: `type` литерал, определяющий выходную схему табличных данных, возвращаемую кодом Python.
    * Формат: `typeof(` *ColumnName* `:` *ColumnType* [,...] `)`, например: `typeof(col1:string, col2:long)`.
    * Для расширения входной схемы используйте следующий синтаксис:`typeof(*, col1:string, col2:long)`
* *Скрипт*: `string` литерал, который является допустимым скриптом Python для выполнения.
* *script_parameters*: Необязательный `dynamic` литерал, который является контейнером свойств пар "имя-значение", передаваемых в скрипт Python в качестве `kargs` зарезервированного словаря (см. раздел [зарезервированные переменные Python](#reserved-python-variables)).
* *Указание. распространение*: необязательное указание на выполнение подключаемого модуля, распределенное по нескольким узлам кластера.
  * Значение по умолчанию — `single`.
  * `single`: Один экземпляр скрипта будет выполняться по всем данным запроса.
  * `per_node`: Если запрос перед распределением блока Python выполняется, экземпляр скрипта будет выполняться на каждом узле по содержащимся в нем данным.
* *external_artifacts*: Необязательный `dynamic` литерал, который является контейнером свойств Name & пар URL-адресов для артефактов, доступных из облачного хранилища, и может быть доступен для использования сценарием во время выполнения.
  * URL-адреса, указанные в этом контейнере свойств, должны:
  * Быть включена в [политику выноски](../management/calloutpolicy.md)кластера.
    2. В общедоступном расположении или предоставьте необходимые учетные данные, как описано в [строках подключения к хранилищу](../api/connection-strings/storage.md).
  * Артефакты становятся доступными для использования сценарием из локального временного каталога, `.\Temp`а имена, указанные в контейнере свойств, используются в качестве имен локальных файлов (см. [Пример](#examples) ниже).
  * Дополнительные сведения см. в [приложении](#appendix-installing-packages-for-the-python-plugin) ниже.

## <a name="reserved-python-variables"></a>Зарезервированные переменные Python

Следующие переменные зарезервированы для взаимодействия между языком запросов Kusto и кодом Python:

* `df`: Входные табличные данные (значения `T` выше) в виде `pandas` кадров данных.
* `kargs`: Значение аргумента *script_parameters* в виде словаря Python.
* `result``pandas` : Кадр данных, созданный скриптом Python, значение которого преобразуется в табличные данные, отправляемые оператору запроса Kusto, который следует за подключаемым модулем.

## <a name="onboarding"></a>Переход

* По умолчанию подключаемый модуль отключен.
* Необходимые условия для включения подключаемого модуля перечислены [здесь](../concepts/sandboxes.md#prerequisites).
* Включите или отключите подключаемый модуль в [портал Azure на вкладке " **Конфигурация** " кластера](https://docs.microsoft.com/azure/data-explorer/language-extensions).

## <a name="notes-and-limitations"></a>Примечания и ограничения

* Образ "песочницы Python" основан на дистрибутиве *Anaconda 5.2.0* с ядром *Python 3,6* .
  Список пакетов можно найти [здесь](http://docs.anaconda.com/anaconda/packages/old-pkg-lists/5.2.0/py3.6_win-64/) (небольшой процент пакетов может быть несовместим с ограничениями, принудительно выполняемыми песочницей, в которой выполняется подключаемый модуль).
* Образ Python также содержит распространенные пакеты машинного `tensorflow`обучения `keras`: `torch`, `hdbscan`, `xgboost` , и другие полезные пакеты.
* Подключаемый модуль импортирует *NumPy* ( `np`как) & *Pandas* ( `pd`AS) по умолчанию.  При необходимости можно импортировать другие модули.
* **Прием [из политик запросов](../management/data-ingestion/ingest-from-query.md) и [обновлений](../management/updatepolicy.md)**
  * Можно использовать подключаемый модуль в запросах, которые:
      1. Определяется как часть политики обновления, исходная таблица которой принимается для использования приема *без потоковой передачи* .
      2. Выполнение в составе команды, принимающей запрос от запроса (например, `.set-or-append`).
  * В обоих случаях рекомендуется убедиться, что объем и частота приема, а также сложность и использование ресурсов логики Python согласованы с [ограничениями песочницы](../concepts/sandboxes.md#limitations)и доступными ресурсами кластера.
    Несоблюдение этого действия может привести к [ошибкам регулирования](../concepts/sandboxes.md#errors).
  * *Невозможно использовать* подключаемый модуль в запросе, который определен как часть политики обновления, исходная таблица которого принимается с помощью [приема потоковой передачи](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming).

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
:::image type="content" source="images/samples/sine-demo.png" alt-text="синусная демонстрация":::

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
    * Чтобы выполнить вычисление для подмножества исходных столбцов, перед вызовом подключаемого модуля следует проецировать только этот столбец.
* Используется `hint.distribution = per_node` каждый раз, когда логика в скрипте является распространяемой.
    * Можно также использовать [оператор Partition](partitionoperator.md) для секционирования входного набора данных.
* Используйте язык запросов Kusto, когда это возможно, чтобы реализовать логику скрипта Python.

    Пример.

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

* Чтобы создать многострочные строки, содержащие скрипт Python в `Kusto.Explorer`, скопируйте скрипт Python из предпочтительного редактора Python (*Jupyter*, *Visual Studio Code*, *PyCharm*и т. д.), а затем выполните одно из следующих действий:
    * Нажмите клавишу *F2* , чтобы открыть окно **изменить в Python** . Вставьте сценарий в это окно. Щелкните **ОК**. Сценарий будет дополнен кавычками и новыми строками (так что он допустим в Kusto) и автоматически вставлен на вкладку запрос.
    * Вставьте код Python непосредственно на вкладку запрос, выберите эти строки и нажмите клавиши *Ctrl + k*, *CTRL + S* , чтобы снабдить их, как показано выше (чтобы изменить его, нажмите клавиши *Ctrl + k*, *клавиши CTRL + M* ). [Ниже](../tools/kusto-explorer-shortcuts.md#query-editor) приведен полный список ярлыков редактора запросов.
* Чтобы избежать конфликтов между разделителями строк Kusto и строковыми литералами Python, мы рекомендуем использовать одинарные кавычки`'`() для строковых литералов Kusto в запросах Kusto и двойные кавычки (`"`) для строковых литералов Python в скриптах Python.
* Используйте [оператор externaldata](externaldata-operator.md) для получения содержимого скрипта, хранящегося во внешнем расположении, например в хранилище BLOB-объектов Azure.
  
    **Пример**

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

## <a name="appendix-installing-packages-for-the-python-plugin"></a>Приложение. Установка пакетов для подключаемого модуля Python

Вам может потребоваться самостоятельно установить пакеты по одной из следующих причин:

* Пакет является частным и собственным.
* Пакет является общедоступным, но не включен в базовый образ подключаемого модуля.

Пакеты можно установить, выполнив следующие действия.

1. Однократное предварительное требование:
  
  a. Создайте контейнер больших двоичных объектов для размещения пакетов, предпочтительно в том же регионе, что и кластер.
    * Пример: `https://artifcatswestus.blob.core.windows.net/python` (если кластер находится в западной части США)
  
  b. Измените [политику выноски](../management/calloutpolicy.md) кластера, чтобы разрешить доступ к этому расположению.
    * Для этого требуются разрешения [аллдатабасесадмин](../management/access-control/role-based-authorization.md) .
    * Например, чтобы разрешить доступ к большому двоичному объекту `https://artifcatswestus.blob.core.windows.net/python`, расположенному в, выполните команду:

      ```kusto
      .alter-merge cluster policy callout @'[ { "CalloutType": "sandbox_artifacts", "CalloutUriRegex": "artifcatswestus\\.blob\\.core\\.windows\\.net/python/","CanCall": true } ]'
      ```

2. Для общедоступных пакетов (в [PyPi](https://pypi.org/) или других каналах) a. Скачайте пакет и его зависимости.
  b. При необходимости Скомпилируйте в файлы колеса (`*.whl`):
    * В окне cmd (в локальной среде Python) выполните следующее:
      ```python
      pip wheel [-w download-dir] package-name.
      ```

3. Создайте ZIP-файл, содержащий необходимый пакет и его зависимости:

    * Для общедоступных пакетов: ZIP-файлы, скачанные на предыдущем шаге.
    * Примечания.
        * Обязательно запишите сами `.whl` файлы, а *не* их родительскую папку.
        * Вы можете пропустить `.whl` файлы для пакетов, которые уже существуют с той же версией в базовом образе песочницы.
    * Для частных пакетов: ZIP-папка пакета и их зависимости

4. Отправьте сжатый ZIP-файл в большой двоичный объект в расположении артефактов (из шага 1).

5. Вызов `python` подключаемого модуля:
    * Укажите `external_artifacts` параметр с контейнером свойств Name и ссылкой на ZIP-файл (URL для большого двоичного объекта).
    * Во встроенном коде Python: импортируйте `Zipackage` из `sandbox_utils` и вызовите `install()` его метод с именем ZIP-файла.

### <a name="example"></a>Пример

Установка пакета [поддельной](https://pypi.org/project/Faker/) информации, который создает фиктивные данные:

```kusto
range Id from 1 to 3 step 1 
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

| Идентификатор | Название         |
|----|--------------|
|   1| Гари Тапиа   |
|   2| Емма Эванс   |
|   3| Ashley БОВЕН |

---

::: zone-end

::: zone pivot="azuremonitor"

Это не поддерживается в Azure Monitor

::: zone-end
