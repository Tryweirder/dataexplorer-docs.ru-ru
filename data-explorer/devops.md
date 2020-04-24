---
title: Задача Azure DevOps для исследователя данных Azure
description: В этой теме вы научитесь создавать конвейер выпуска и развертывать
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: ced839f671a94744799e8a6cde4dbee2a8f17cc3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498140"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Задача Azure DevOps для исследователя данных Azure

[Службы Azure DevOps](https://azure.microsoft.com/services/devops/) предоставляют инструменты совместной работы, такие как высокопроизводительные конвейеры, бесплатные частные репозитории Git, настраиваемые доски Kanban, а также обширные возможности автоматизированного и непрерывного тестирования. [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) — это возможность Azure DevOps, которая позволяет управлять CI/CD для развертывания кода с высокопроизводительными конвейерами, которые работают с любым языком, платформой и облаком.
[Команды Azure Data Explorer - Admin Commands](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) — задача конвейеров Azure, которая позволяет создавать конвейеры выпусков и развертывать изменения базы данных в базах данных Azure Data Explorer. Он доступен бесплатно в [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

В этом документе описывается простой пример использования задачи Azure Data Explorer — задачи **команд овеки администрирования** для развертывания изменений схемы в базе данных. Для получения полных конвейеров CI/CD обратитесь к [документации Azure DevOps.](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts)

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* Настройка кластера Azure Data Explorer:
    * [Кластер и база данных Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal)
    * Создайте приложение Azure Active Directory (Azure AD), [подготавив приложение Azure AD.](kusto/management/access-control/how-to-provision-aad-app.md)
    * Предоставьте доступ к приложению Azure AD в базе данных Azure Data Explorer, [управляя разрешениями базы данных Azure Data Explorer.](/azure/data-explorer/manage-database-permissions)
* Настройка Azure DevOps:
    * [Подпишитесь на бесплатную организацию](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Создание организации](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Создание проекта в Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Код с Git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Создайте папки:

Создайте следующие примеры папок *(Функции,* *Политики,* *Таблицы)* в репозитории Git. Копировать файлы [отсюда](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) в соответствующие папки, как показано ниже, и совершить изменения. Файлы выборки предоставляются для выполнения следующего рабочего процесса.

![Создайте папки:](media/devops/create-folders.png)

> [!TIP]
> При создании собственного рабочего процесса мы рекомендуем сделать ваш код идемпотент. Например, используйте [таблицу .create-merge](kusto/management/create-table-command.md#create-merge-table) вместо [таблицы .create,](kusto/management/create-table-command.md)и используйте функцию [.create-or-alter](kusto/management/create-alter-function.md) вместо функции [.create.](kusto/management/create-function.md)

## <a name="create-a-release-pipeline"></a>Создание конвейера выпуска

1. Вопийте в [организации Azure DevOps.](https://dev.azure.com/)
1. Выберите**выпуски** **трубопроводов** > из меню левого гостевого и выберите **новый конвейер.**

    ![Новый конвейер](media/devops/new-pipeline.png)

1. Открывается **окно нового конвейера выпуска.** Во вкладке **Pipelines** во панели **шаблона Выберите** **пустое задание.**

     ![Выберите шаблон](media/devops/select-template.png)

1. Выберите кнопку **Этап.** В **панели этапа** добавьте имя **сцены**. Выберите **Сохранить,** чтобы сохранить конвейер.

    ![Назовите сцену](media/devops/stage-name.png)

1. Выберите **кнопку «Добавить артефакт».** В панели **добавления артефакта** выберите репозиторий, в котором существует ваш код, заполните соответствующую информацию и нажмите **«Добавить».** Выберите **Сохранить,** чтобы сохранить конвейер.

    ![Добавление артефакта](media/devops/add-artifact.png)

1. Во вкладке **Переменные** выберите **и добавьте** для создания переменной для **URL-адреса Endpoint,** которая будет использоваться в задаче. Напишите **имя** и **значение** конечной точки. Выберите **Сохранить,** чтобы сохранить конвейер. 

    ![Создание переменной](media/devops/create-variable.png)

    Чтобы найти Endpoint_URL, страница обзора **кластера Azure Data Explorer на** портале Azure содержит кластер URI Explorer данных Azure. Постройте URI в `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`следующем формате.  Например, https:\//kustodocs.westus.kusto.windows.net?DatabaseName-SampleDB

    ![Кластер Исследователя данных Azure URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Создание задач для развертывания

1. Во вкладке **"Конвейер"** нажмите на **1 задание 0,** чтобы добавить задачи. 

    ![Добавление задач](media/devops/add-task.png)

1. Создайте три задачи для развертывания **таблиц,** **функций**и **политик**в этом порядке. 

1. Во вкладке **+** **"Задачи"** выберите **задание агента.** Найдите **Azure Data Explorer**. В **Marketplace**установите расширение **Azure Data Explorer — Admin Commands.** Затем **выберите Добавление** в **команду исследователя данных Run Azure.**

     ![Добавление команд админа](media/devops/add-admin-commands.png)

1. Нажмите на **команду Кусто** слева и обновите задачу следующей информацией:
    * **Имя дисплея**: Имя задачи
    * **Путь файла**: В **задаче таблицы,** укажите */Таблицы/*.csl, так как файлы создания таблицы находятся в папке *таблицы.*
    * **URL-адреса конечных точек**: введите переменную, `EndPoint URL`созданную на предыдущем этапе.
    * Выберите **конечную точку службы использования** и выберите **новый**.

    ![Обновление командной задачи Kusto](media/devops/kusto-command-task.png)

1. Заполните следующую информацию в окне **подключения службы Add Azure Data Explorer:**

    |Параметр  |Рекомендуемое значение  |
    |---------|---------|
    |**Имя соединения**     |    Введите имя, чтобы определить конечную точку службы     |
    |**Кластер Url**    |    Значение можно найти в разделе обзора кластера Azure Data Explorer на портале Azure | 
    |**Директор службы Id**    |    Введите идентификатор приложения AAD (созданный в качестве предварительного условия)     |
    |**Ключевое приложение службы**     |    Введите ключ приложения AAD (созданный в качестве предварительного условия)    |
    |**AAD арендатор id**    |      Введите ваш AAD арендатора (например, microsoft.com, contoso.com ...)    |

    Выберите **Разрешить всем конвейерам использовать этот** флажок соединения. Нажмите кнопку **ОК**.

    ![Добавление подключения к службе](media/devops/add-service-connection.png)

1. Повторите шаги 1-5 еще два раза для развертывания файлов из папок *функций* и *политик.* Щелкните **Сохранить**. Во вкладке **«Задачи»** см. три созданные задачи: **таблицы развертывания,** **функции развертывания**и **политики развертывания.**

    ![Развертывание всех папок](media/devops/deploy-all-folders.png)

1. Выберите **релиз** > для создания**релиза.**

    ![Создание выпуска](media/devops/create-release.png)

1. Во вкладке **Журналы** проверьте успешное состояние развертывания.

    ![Развертывание успешно](media/devops/deployment-successful.png)

В настоящее время завершено создание конвейера выпуска для развертывания трех задач для предварительного производства.