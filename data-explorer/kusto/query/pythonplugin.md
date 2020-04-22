---
title: Плагин Python - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается плагин Python в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5192474779fb712595ff1c25785892bb543fda84
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765716"
---
# <a name="python-plugin"></a>Плагин Python

::: zone pivot="azuredataexplorer"

Плагин Python запускает пользовательскую функцию (UDF) с помощью скрипта Python. Скрипт Python получает табелярные данные в качестве ввода и, как ожидается, будет производить таблетчатый вывод.
Время выполнения плагина размещается в [песочницах,](../concepts/sandboxes.md)работающих на узлах кластера.

## <a name="syntax"></a>Синтаксис

*Т* `|` `python(` *output_schema* `,` *script_parameters*`,` *external_artifacts* *script* . `=` `single`( | `,` ) output_schema скрипт , script_parameters external_artifacts`per_node``hint.distribution` `evaluate``)`

## <a name="arguments"></a>Аргументы

* *output_schema*: `type` буквальный, определяющий схему вывода табликовых данных, возвращенную кодом Python.
    * Формат: `typeof(` *ColumnName* `:` *ColumnType* ,,... `)`, например: . `typeof(col1:string, col2:long)`
    * Для расширения схемы ввода используйте следующий синтаксис:`typeof(*, col1:string, col2:long)`
* *скрипт* `string` : Буквальный, который является действительным скриптом Python, который будет выполнен.
* *script_parameters*: `dynamic` Дополнительный буквальный, который представляет собой пакет свойств пар имен/значений, `kargs` которые должны быть переданы скрипту Python в качестве зарезервированного словаря (см. [Зарезервированные переменные Python).](#reserved-python-variables)
* *hint.distribution*: Дополнительный намек на выполнение плагина, который будет распределен между несколькими кластерными узлами.
  * Значение по умолчанию — `single`.
  * `single`: Один экземпляр скрипта будет работать по всем данным запроса.
  * `per_node`: Если запрос перед распространением блока Python будет распределен, экземпляр скрипта будет работать на каждом узлах над содержащимися в нем данными.
* *external_artifacts*: `dynamic` Дополнительный буквальный, который представляет собой пакет с именем & URL-адреса для пар артефактов, которые доступны из облачного хранилища и могут быть доступны для использования скрипта во время выполнения.
  * URL-адреса, упомянутые в этом пакете свойств, должны:
  * Включите в [политику вызова](../management/calloutpolicy.md)кластера.
    2. Находясь в общедоступном месте или предоставить необходимые учетные данные, как это объясняется в [строках подключения хранения.](../api/connection-strings/storage.md)
  * Артефакты доступны для использования скрипта из локального временного `.\Temp`каталога, а имена, приведенные в пакете свойств, используются в качестве локальных имен файлов (см. [пример](#examples) ниже).
  * Для получения дополнительной информации смотрите [приложение](#appendix-installing-packages-for-the-python-plugin) ниже.

## <a name="reserved-python-variables"></a>Зарезервированные переменные Python

Для взаимодействия между языком запросов Kusto и кодом Python зарезервированы следующие переменные:

* `df`: Входные табелярные данные `T` (значения `pandas` выше), как DataFrame.
* `kargs`: Значение *script_parameters* аргумента, как словарь Python.
* `result`: `pandas` DataFrame, созданный скриптом Python, значение которого становится табелярными данными, которые отправляются оператору запроса Kusto, который следует за плагином.

## <a name="onboarding"></a>Переход

* Плагин отключен по умолчанию.
* Предпосылки для включения плагина перечислены [здесь.](../concepts/sandboxes.md#prerequisites)
* Включить или отключить плагин [на портале Azure во вкладке **Конфигурация** кластера.](https://docs.microsoft.com/azure/data-explorer/language-extensions)

## <a name="notes-and-limitations"></a>Заметки и ограничения

* Изображение песочницы Python основано на распределении *Anaconda 5.2.0* с двигателем *Python 3.6.*
  Список его пакетов можно найти [здесь](http://docs.anaconda.com/anaconda/packages/old-pkg-lists/5.2.0/py3.6_win-64/) (небольшой процент пакетов может быть несовместим с ограничениями, наменяемыми песочницей, в которой работает плагин).
* Изображение Python также содержит общие `tensorflow`пакеты `torch` `hdbscan`ML: , `xgboost` `keras`, , и другие полезные пакеты.
* Плагин импортирует *numpy* (как `np`) `pd`& *панды* (как ) по умолчанию.  Вы можете импортировать другие модули по мере необходимости.
* **[Прогласивание из](../management/data-ingestion/ingest-from-query.md) [политик запросов](../management/updatepolicy.md) и обновления**
  * Можно использовать плагин в запросах, которые являются:
      1. Определяется как часть политики обновления, исходная таблица которой попадает в систему с *использованием непотокового* приема.
      2. Выполнить как часть команды, которая глотает из запроса `.set-or-append`(например).
  * В обоих вышеуказанных случаях рекомендуется проверить, что объем и частота приема, а также сложность и использование ресурсов логики Python приведены в соответствие с [ограничениями песочницы](../concepts/sandboxes.md#limitations)и имеющимися ресурсами кластера.
    Невыполнение этого требования может привести к [ошибкам регулирования.](../concepts/sandboxes.md#errors)
  * Нельзя *not* использовать плагин в запросе, который определяется как часть политики обновления, исходная таблица которой попадает в потоксот. [streaming ingestion](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming)

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
:::image type="content" source="images/samples/sine-demo.png" alt-text="синусоида демо":::

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

* Уменьшите набор входных данных плагина до необходимого минимума (столбцы/ряды).
    * Используйте фильтры на наборе исходных данных, когда это возможно, с языком запроса Kusto.
    * Чтобы выполнить расчет на подмножестве исходных столбцов, проецируйте только те столбцы, прежде чем высвунуть плагин.
* Используйте `hint.distribution = per_node` всякий раз, когда логика в скрипте утилизна.
    * Оператор [раздела](partitionoperator.md) также может использоваться для раздела набора входных данных.
* Используйте язык запросов Kusto, когда это возможно, чтобы реализовать логику вашего сценария Python.

    Пример

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

* Для создания многострочных строк, содержащих `Kusto.Explorer`сценарий Python в , скопировать сценарий Python из вашего любимого редактора Python *(Jupyter*, *Visual Studio Code*, *PyCharm*и т.д.), то либо:
    * Нажмите *F2,* чтобы открыть **edit в** окне Python. Вставьте скрипт в это окно. Щелкните **ОК**. Скрипт будет украшен кавычками и новыми строками (так что он действителен в Кусто) и автоматически вклеен в вкладку запроса.
    * Вставьте код Python непосредственно в вкладку запроса, выберите эти строки и нажмите *Ctrl-K*, *Ctrl-S* горячий ключ, чтобы украсить их, как выше (чтобы обратить его нажмите *Ctrl'K*, *Ctrl-M* горячий ключ). [Вот](../tools/kusto-explorer-shortcuts.md#query-editor) полный список ярлыков редактора запросов.
* Чтобы избежать конфликтов между делимитаторами строкku to string и Python, мы`'`рекомендуем использовать отдельные символы цитаты () для буквальных`"`букватов Строки Kusto в запросах Kusto и двойные символы цитаты () для строк Python в буквальных строках Python в сценариях Python.
* Используйте [оператора внешних данных](externaldata-operator.md) для получения содержимого скрипта, который вы сохранили во внешнем месте, например в хранилище Azure Blob.
  
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

## <a name="appendix-installing-packages-for-the-python-plugin"></a>Приложение: Установка пакетов для плагина Python

Возможно, вам придется самостоятельно установить пакет (ы) из-за любой из следующих причин:

* Пакет является частным и является вашим собственным.
* Пакет является общедоступным, но не включен в базовое изображение плагина.

Вы можете установить пакеты, выдвивая следующие действия:

1. Одноразовое предварительное условие:
  
  а. Создайте контейнер с каплями для размещения пакета (ы), предпочтительно в том же регионе, что и кластер.
    * Например: https://artifcatswestus.blob.core.windows.net/python (при условии, что ваш кластер находится на западе США)
  
  b. Измените политику [вызова](../management/calloutpolicy.md) кластера, чтобы получить доступ к этому местоположению.
    * Для этого требуется разрешение [AllDatabasesAdmin.](../management/access-control/role-based-authorization.md)
    * Например, для обеспечения доступа к https://artifcatswestus.blob.core.windows.net/pythonкаплей, расположенной в, команда для запуска:

      ```kusto
      .alter-merge cluster policy callout @'[ { "CalloutType": "sandbox_artifacts", "CalloutUriRegex": "artifcatswestus\\.blob\\.core\\.windows\\.net/python/","CanCall": true } ]'
      ```

2. Для общедоступных пакетов (в [PyPi](https://pypi.org/) или других каналах) a. Скачать пакет и его зависимостей.
  b. При необходимости компилировать`*.whl`на колесо () файлы:
    * Из окна cmd (в локальной среде Python) выполнить:
      ```python
      pip wheel [-w download-dir] package-name.
      ```

3. Создайте почтовый файл, содержащий необходимый пакет и его зависимости:

    * Для общедоступных пакетов: застегните файлы, которые были загружены на предыдущем этапе.
    * Примечания.
        * Убедитесь в `.whl` том, чтобы застегнуть файлы сами, а *не* их родителей папку.
        * Вы можете `.whl` пропустить файлы для пакетов, которые уже существуют с той же версией в базовой песочнице изображения.
    * Для частных пакетов: застегните папку пакета и папку его зависимостей

4. Загрузите файл на молнии в каплю в месте артефактов (от шага 1.).

5. Вызов `python` плагина:
    * Укажите `external_artifacts` параметр с пакетом недвижимости с именем и ссылкой на почтовый файл (URL-адрес blob).
    * В вашем вливанном `Zipackage` `sandbox_utils` коде `install()` питона: импортируйте и звоните его методу с названием файла застежки-молнии.

### <a name="example"></a>Пример

Установка пакета [Faker,](https://pypi.org/project/Faker/) который генерирует поддельные данные:

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

| Идентификатор | Имя         |
|----|--------------|
|   1| Гэри Тапиа   |
|   2| Эмма Эванс   |
|   3| Эшли Боуэн |

---

::: zone-end

::: zone pivot="azuremonitor"

Это не поддерживается в Azure Monitor

::: zone-end
