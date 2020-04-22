---
title: Используйте ноутбуки Azure для анализа данных в Azure Data Explorer
description: Эта тема показывает, как создать запрос с помощью ноутбука Azure
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f99e11be99f22feec73b72397b27522b90dbf49
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496268"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>Используйте ноутбуки Azure для анализа данных в Azure Data Explorer

[Блокноты Azure](https://notebooks.azure.com/) — это облачный сервис Azure, который упрощает создание и совместное использование [ноутбуков Jupyter.](https://jupyter.org/) Блокноты Azure упрощают объединение документации, кода и результатов работы кода.

> [!Note]
> * Следующая процедура использует клиента Python в среде блокнотов Azure для запроса Azure Data Explorer. Тем не менее, можно также использовать [K'Lmagic](https://docs.microsoft.com/azure/data-explorer/kqlmagic) для запроса Azure Data Explorer.
> * Некоторые пользователи сообщили о проблемах аутентификации с помощью Edge; до тех пор, пока такие проблемы не будут решены, используйте другой браузер.

## <a name="create-a-project"></a>Создание проекта

1. Откройте [ноутбуки Azure](https://notebooks.azure.com/) в браузере и вопийте.

1. Выберите вкладку **«Мои проекты»** в заголовке. 

    [![](media/azurenotebooks/an-myprojects.png "My projects")](media/azurenotebooks/an-myprojects.png#lightbox)

1. Выберите **новые проекты**.
    
1. В диалоговом окне **«Создать новый проект»:**
    1. Введите **название проекта**.
    1. Очистить **общественный** флажок.
        >[!Important]
        > Если вы не очистите открытый флажок, ваш проект будет выставлен в открытом Интернете.
    1. Нажмите кнопку **создания**.
    
    ![Создание нового проекта](media/azurenotebooks/an-create-new-project-blank.png)

    Идентификатор проекта создается автоматически.

## <a name="create-a-notebook"></a>Создание записной книжки

1. В новом проекте создайте блокнот. Ноутбук должен использовать [поддерживаемый язык.](https://github.com/Azure/azure-kusto-python#minimum-requirements)
Чтобы создать блокнот, выберите **новый** и выберите **ноутбук**.

    ![Создание нового ноутбука](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. В диалоговом **окне Создать новый блокнот** введите имя ноутбука.

1. Выберите **Python 3.6** и выберите **новый**.
    
    ![Создание нового ноутбука](media/azurenotebooks/an-create-new-notebook.png) 
    
1. В проекте выберите новый блокнот.

    ![Выберите новый блокнот](media/azurenotebooks/an-select-notebook.png)

    Подождите, пока ядро Python инициализируется. Когда значок заполненного круга изменяется на значок полого круга, вы можете продолжить.

    ![Инициализирует ядро](media/azurenotebooks/an-python-init-icon.png)

1. Для импорта системного модуля введите следующие команды и выберите **Run:**
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > Для запуска ячейки можно также нажать Shift-Enter.

1.  Чтобы импортировать классы SDK, введите следующие команды и выберите **Run:**
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  Чтобы построить строку соединения и запустить клиент Kusto, введите следующие команды и выберите **Выполнить:**  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. В подсказке, откройте новую вкладку браузера для [https://aka.ms/devicelogin](https://aka.ms/devicelogin). 
   
1. Введите код авторизации и войдите@microsoft.comв свою учетную запись AAD ( ) Клиент Kusto `kc` теперь может проверить подлинность в Azure Data Explorer с помощью вашей идентификации.

1. Вернитесь в блокнот, чтобы увидеть результат проверки подлинности. 

[![](media/azurenotebooks/an-python-commands.png "Python commands")](media/azurenotebooks/an-python-commands.png#lightbox)

## <a name="execute-a-kusto-query"></a>Выполнить запрос Kusto

1. Введите свой запрос Kusto и выберите **Run.** Пример:

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

[![](media/azurenotebooks/an-commands.png "Python commands")](media/azurenotebooks/an-commands.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

* [Кусто-питон GitHub репо](https://github.com/Azure/azure-kusto-python)
