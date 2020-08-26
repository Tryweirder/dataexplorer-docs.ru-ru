---
title: Использование записных книжек Azure для анализа данных в Azure обозреватель данных
description: В этом разделе показано, как создать запрос с помощью записной книжки Azure.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/01/2020
ms.openlocfilehash: d41c9e2719ef5139d89986ff5333c03e6e34d26c
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872477"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>Использование записных книжек Azure для анализа данных в Azure обозреватель данных

[Записные книжки Azure](https://notebooks.azure.com/) — это облачная служба Azure, которая упрощает создание и совместное использование [записных книжек Jupyter](https://jupyter.org/). Записные книжки Azure позволяют легко объединять документацию, код и результаты выполнения кода.

> [!Note]
> * Следующая процедура использует клиент Python в среде записных книжек Azure для запроса обозреватель данных Azure. Тем не менее можно также использовать [кклмагик](kqlmagic.md) для запроса обозреватель данных Azure.
> * Некоторые пользователи сообщили о проблемах при проверке подлинности с помощью ребра; пока такие проблемы не будут устранены, используйте другой браузер.

## <a name="create-a-project"></a>Создание проекта

1. Откройте [записные книжки Azure](https://notebooks.azure.com/) в браузере и войдите в систему.

1. Выберите вкладку **Мои проекты** в заголовке. 

    :::image type="content" source="media/azurenotebooks/an-myprojects.png" alt-text="Страница "проекты", вкладка "Мои проекты", Записные книжки Microsoft Azure, Azure обозреватель данных" lightbox="media/azurenotebooks/an-myprojects.png#lightbox":::

1. Выберите **+ создать проекты**.
    
1. В диалоговом окне **Создание нового проекта** выполните следующие действия.
    1. Введите **имя проекта**.
    1. Снимите флажок **Public (общедоступный** ).
        >[!Important]
        > Если не снять флажок общедоступный, ваш проект будет открыт в Интернете.
    1. Нажмите кнопку **создания**.
    
    ![Создание нового проекта](media/azurenotebooks/an-create-new-project-blank.png)

    ИДЕНТИФИКАТОР проекта создается автоматически.

## <a name="create-a-notebook"></a>Создание записной книжки

1. В новом проекте создайте записную книжку. В записной книжке должен использоваться [поддерживаемый язык](https://github.com/Azure/azure-kusto-python#minimum-requirements).
Чтобы создать записную книжку, щелкните **+ создать** и выберите **Записная книжка**.

    ![Создать записную книжку](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. В диалоговом окне **Создание новой записной книжки** введите имя записной книжки.

1. Выберите **Python 3,6** и нажмите кнопку **создать**.
    
    ![Создать записную книжку](media/azurenotebooks/an-create-new-notebook.png) 
    
1. В проекте выберите новую записную книжку.

    ![Выбрать новую записную книжку](media/azurenotebooks/an-select-notebook.png)

    Дождитесь инициализации ядра Python. Когда значок с заполненным кружком превращается в пустой значок круга, можно продолжать.

    ![Инициализация ядра](media/azurenotebooks/an-python-init-icon.png)

1. Чтобы импортировать модуль системы, введите следующие команды и выберите **выполнить**:
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > Чтобы выполнить ячейку, можно также нажать клавиши SHIFT + ВВОД.

1.  Чтобы импортировать классы SDK, введите следующие команды и выберите **выполнить**:
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  Чтобы создать строку подключения и запустить клиент Kusto, введите следующие команды и выберите **выполнить**:  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. В командной строке откройте новую вкладку браузера в [https://aka.ms/devicelogin](https://aka.ms/devicelogin) . 
   
1. Введите код авторизации и войдите в @microsoft.com учетную запись AAD (). Теперь клиент Kusto `kc` может пройти проверку подлинности в Azure обозреватель данных с помощью удостоверения.

1. Вернитесь к записной книжке, чтобы увидеть результат проверки подлинности. 

:::image type="content" source="media/azurenotebooks/an-python-commands.png" alt-text="Вывод результатов проверки подлинности, окно записной книжки, Записные книжки Microsoft Azure, Azure обозреватель данных" lightbox="media/azurenotebooks/an-python-commands.png#lightbox":::

## <a name="execute-a-kusto-query"></a>Выполнение запроса Kusto

1. Введите запрос Kusto и выберите **Run (выполнить**). Пример.

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

:::image type="content" source="media/azurenotebooks/an-commands.png" alt-text="Кнопка "выполнить", окно "Записная книжка", Записные книжки Microsoft Azure, Azure обозреватель данных" lightbox="media/azurenotebooks/an-commands.png#lightbox":::

## <a name="next-steps"></a>Дальнейшие шаги

* [Kusto-репозиторий GitHub Python](https://github.com/Azure/azure-kusto-python)
