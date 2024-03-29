---
title: Создание кластера Azure обозреватель данных & DB с Azure CLI
description: Сведения о создании кластера и базы данных Azure Data Explorer с помощью Azure CLI
author: orspod
ms.author: orspodek
ms.reviewer: radennis
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: 4098f8bd6992ce5f76bfd3f611ba4d4bad2374b3
ms.sourcegitcommit: 79d923d7b7e8370726974e67a984183905f323ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96868660"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Создание кластера и базы данных Azure Data Explorer с помощью Azure CLI

> [!div class="op_single_selector"]
> * [Портал](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [GO](create-cluster-database-go.md)
> * [Шаблон ARM](create-cluster-database-resource-manager.md)

Azure Data Explorer — это быстрая и полностью управляемая служба для аналитики большого объема потоковых данных в реальном времени, поступающих из приложений, а также с веб-сайтов, устройств Интернета вещей и т. д. Чтобы использовать обозреватель данных Azure, сначала нужно создать кластер и одну или несколько баз данных в этом кластере. Затем вы должны принять (загрузить) данные в базы данных, чтобы к ним можно было выполнять запросы. В этой статье вы создадите кластер и базу данных с помощью Azure CLI.

## <a name="prerequisites"></a>Предварительные условия

Для выполнения этой статьи требуется подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начать работу.

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать Azure CLI локально, для работы с этой статьей требуется Azure CLI версии 2.0.4 или более поздней. Выполните команду `az --version`, чтобы узнать номер версии. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

## <a name="configure-the-cli-parameters"></a>Настройка параметров CLI

Следующие действия не требуются, если вы выполняете команды в Azure Cloud Shell. Если вы используете CLI локально, выполните следующие действия, чтобы войти в учетную запись Azure и выбрать текущую подписку:

1. Выполните следующую команду, чтобы войти в Azure:

    ```azurecli-interactive
    az login
    ```

1. Укажите подписку, в которой необходимо создать кластер. Замените `MyAzureSub` именем подписки Azure, которую вы хотите использовать:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```
   
1. Установите расширение, чтобы использовать последнюю версию Kusto CLI:

    ```azurecli-interactive
    az extension add -n kusto
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Создание кластера Azure Data Explorer

1. Создайте кластер, используя приведенную ниже команду:

    ```azurecli-interactive
    az kusto cluster create --cluster-name azureclitest --sku name="Standard_D13_v2" tier="Standard" --resource-group testrg --location westus
    ```

   |**Параметр** | **Рекомендуемое значение** | **Описание поля**|
   |---|---|---|
   | name | *azureclitest* | Необходимое имя кластера.|
   | sku | *Standard_D13_v2* | Номер SKU, который будет использоваться для кластера. Parameters: *Name* — имя SKU. *уровень* — уровень SKU. |
   | resource-group | *testrg* | Имя группы ресурсов, в которой будет создан кластер. |
   | location | *westus* | Расположение, в котором будет создан кластер. |

    Можно использовать дополнительные необязательные параметры, например емкость кластера и т. д.

1. Чтобы проверить, успешно ли создан кластер, можно выполнить следующую команду:

    ```azurecli-interactive
    az kusto cluster show --cluster-name azureclitest --resource-group testrg
    ```

Если результат содержит параметр `provisioningState` со значением `Succeeded`, это означает, что кластер создан успешно.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Создание базы данных в кластере Azure Data Explorer

1. Создайте базу данных, используя приведенную ниже команду:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --database-name clidatabase --resource-group testrg --read-write-database soft-delete-period=P365D hot-cache-period=P31D location=westus
    ```

   |**Параметр** | **Рекомендуемое значение** | **Описание поля**|
   |---|---|---|
   | cluster-name | *azureclitest* | Имя кластера, в котором необходимо создать базу данных.|
   | имя базы данных | *clidatabase* | Имя базы данных.|
   | resource-group | *testrg* | Имя группы ресурсов, в которой будет создан кластер. |
   | чтение и запись базы данных | *P365D* *P31D* *westus* | Тип базы данных. Параметры: *обратимое удаление — период* — указывает время, в течение которого данные будут храниться в запросе. Дополнительные сведения см. в статье [Политика хранения](kusto/management/retentionpolicy.md). " *горячий" кэш — период* — указывает время, в течение которого данные будут храниться в кэше. Дополнительные сведения см. в статье [Политика кэша](kusto/management/cachepolicy.md). *Location* — расположение, в котором будет создана база данных. |

1. Выполните следующую команду, чтобы просмотреть созданную базу данных:

    ```azurecli-interactive
    az kusto database show --database-name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Теперь у вас есть кластер и база данных.

## <a name="clean-up-resources"></a>Очистка ресурсов

* Если вы планируете следовать нашим другим статьям, не заполняйте созданные ресурсы.
* Чтобы очистить ресурсы, удалите кластер. При удалении кластера также удаляются все содержащиеся в нем базы данных. Для удаления кластера используйте следующую команду:

    ```azurecli-interactive
    az kusto cluster delete --cluster-name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Дальнейшие действия

* [Прием данных с помощью библиотеки Azure обозреватель данных Python](python-ingest-data.md)
