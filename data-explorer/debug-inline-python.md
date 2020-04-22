---
title: Debug Kusto язык запроса вонючий Python с помощью VS Code - Azure Data Explorer
description: Узнайте, как отладить язык запроса Kusto (K'L) встроенного Python с помощью VS Code.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 808d51ff5bc070449c87d14822fe4f41d2038950
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81499102"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Debug Kusto язык запроса вонючий Python с помощью VS code

Azure Data Explorer поддерживает запуск кода Python, встроенного в язык запроса Kusto, с помощью [плагина python()](kusto/query/pythonplugin.md). Время выполнения плагина размещается в песочнице, изолированной и безопасной среде Python. Возможность плагина Python() расширяет возможности языка запроса Kusto с помощью огромного архива пакетов OSS Python. Это расширение позволяет запускать передовые алгоритмы, такие как машинное обучение, искусственный интеллект, статистические и временные ряды в рамках запроса.

Инструменты языка запросов Kusto не удобны для разработки и отладки алгоритмов Python. Поэтому разработайте алгоритм в вашей любимой интегрированной среде разработки Python, такой как Jupyter, PyCharm, VS или VS Code. Когда алгоритм будет завершен, скопируйте и вставьте в КЗЛ. Чтобы улучшить и упорядочить этот рабочий процесс, Azure Data Explorer поддерживает интеграцию между клиентами Kusto Explorer или Web UI и VS Code для авторизации и отладки входящего кода Python. 

> [!NOTE]
> Этот рабочий процесс может использоваться только для отладки относительно небольших таблиц ввода (до нескольких МБ). Поэтому может потребоваться ограничить входные входные связи для отладки.  Если вам нужно обработать большую таблицу, `| take`ограничьте ее для отладки с помощью, `| sample`или `where rand() < 0.x`.

## <a name="prerequisites"></a>Предварительные требования

1. Установка Python [Anaconda Распределение](https://www.anaconda.com/distribution/#download-section). В **расширенных опциях**выберите **Добавить Anaconda в мою переменную среды PATH.**
2. Установка [визуального кода студии](https://code.visualstudio.com/Download)
3. Установите [расширение Python для визуального кода studio](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

## <a name="run-your-query-in-your-client-application"></a>Запустите запрос в клиентском приложении

1. В клиентском приложении префикс запрос, содержащий в строке Python с`set query_python_debug;`
1. Выполните запрос.
    * Kusto Explorer: VS Code автоматически запускается с помощью сценария *debug_python.py.*
    * Кусто веб-uI: 
        1. Скачать и сохранить *debug_python.py*, *df.txt*, и *kargs.txt*. В окне выберите **Разрешить**. **Сохранение** файлов в выбранном каталоге. 

            ![Веб-uI загружает внеочередные файлы питона](media/debug-inline-python/webui-inline-python.png)

        1. Нажмите справа *debug_python.py* и откройте с кодом VS. 
        Скрипт *debug_python.py* содержит внережимный код Python, от запроса КЗЛ, прикреплённый кодом шаблона для инициализации вхотворного кадра данных от *df.txt* и словаря параметров от *kargs.txt.*    
            
1. В коде VS запустите отладчик кода VS: **Отладка** > **начала отладки (F5)**, выберите конфигурацию **Python.** Отладчик запустит и автоматически разрушитель для отладки вневого кода.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Как работает отладка в режиме inline Python в VS Code?

1. Запрос разбирается и выполняется на сервере `| evaluate python()` до достижения требуемой оговорки.
1. Вызов песочницы Python, но вместо запуска кода, он сериализирует таблицу ввода, словарь параметров и код, и отправляет их обратно клиенту.
1. Эти три объекта сохраняются в трех файлах: *df.txt*, *kargs.txt*и *debug_python.py* в выбранном каталоге (Web UI) или в каталоге клиента%TEMP% (Kusto Explorer).
1. VS код запущен, предварительно *загруженный с debug_python.py* файл, который содержит код префикса для инициализации df и карги из своих файлов, а затем сценарий Python, встроенный в запрос КЗЛ.

## <a name="query-example"></a>Пример запроса

1. Запустите следующий запрос КЗЛ в клиентском приложении:

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Смотрите полученную таблицу:

    | x  | x4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Запустите тот же запрос КЗЛ в `set query_python_debug;`клиентском приложении с помощью:

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. Запущен КОД VS:

    ![запуск VS кода](media/debug-inline-python/launch-vs-code.png)

1. ОТладка VS Кода и отпечатки «результата» фрейма данных в консоли отладки:

    ![ОТладка кода VS](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Могут быть различия между изображением песочницы Python и локальной установкой. [Проверьте изображение песочницы для определенных пакетов, задав запрос плагина.](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl)
