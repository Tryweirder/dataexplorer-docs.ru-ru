---
title: Создание кластера Azure обозреватель данных & DB с помощью PowerShell
description: Сведения о создании кластера и базы данных Azure Data Explorer с помощью PowerShell
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: 9d2e50e54d4a33b0648760fc4df395c9686904e9
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92902571"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Создание кластера и базы данных Azure Data Explorer с помощью PowerShell

> [!div class="op_single_selector"]
> * [Портал](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [GO](create-cluster-database-go.md)
> * [Шаблон ARM](create-cluster-database-resource-manager.md)  

Azure Data Explorer — это быстрая и полностью управляемая служба для аналитики большого объема потоковых данных в реальном времени, поступающих из приложений, а также с веб-сайтов, устройств Интернета вещей и т. д. Чтобы использовать обозреватель данных Azure, сначала нужно создать кластер и одну или несколько баз данных в этом кластере. Затем вы должны принять (загрузить) данные в базы данных, чтобы к ним можно было выполнять запросы. В этой статье вы создадите кластер и базу данных с помощью PowerShell. Вы можете выполнять командлеты и скрипты PowerShell в системе Windows и Linux или в [Azure Cloud Shell](/azure/cloud-shell/overview) с помощью [Az.Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) для создания и настройки кластеров и баз данных Azure Data Explorer.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [updated-for-az](includes/updated-for-az.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать Azure CLI локально, для работы с этой статьей требуется Azure CLI версии 2.0.4 или более поздней. Выполните команду `az --version`, чтобы узнать номер версии. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Настройка параметров

Следующие действия не требуются, если вы выполняете команды в Azure Cloud Shell. Если вы используете CLI локально, выполните шаги 1 и 2, чтобы войти в учетную запись Azure и выбрать текущую подписку:

1. Выполните следующую команду, чтобы войти в Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Укажите подписку, в которой необходимо создать кластер.

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. При запуске интерфейса командной строки Azure локально или в Azure Cloud Shell вам потребуется установить модуль Az.Kusto на своем устройстве.

    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Создание кластера Azure Data Explorer

1. Создайте кластер, используя приведенную ниже команду:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Параметр** | **Рекомендуемое значение** | **Описание поля**|
   |---|---|---|
   | Имя | *mykustocluster* | Необходимое имя кластера.|
   | Sku | *D13_v2* | Номер SKU, который будет использоваться для кластера. |
   | ResourceGroupName | *testrg* | Имя группы ресурсов, в которой будет создан кластер. |

    Можно использовать дополнительные необязательные параметры, например емкость кластера и т. д.

1. Чтобы проверить, успешно ли создан кластер, можно выполнить следующую команду:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

Если результат содержит параметр `provisioningState` со значением `Succeeded`, это означает, что кластер создан успешно.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Создание базы данных в кластере Azure Data Explorer

1. Создайте базу данных, используя приведенную ниже команду:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Параметр** | **Рекомендуемое значение** | **Описание поля**|
   |---|---|---|
   | ClusterName | *mykustocluster* | Имя кластера, в котором необходимо создать базу данных.|
   | Имя | *mykustodatabase* | Имя базы данных.|
   | ResourceGroupName | *testrg* | Имя группы ресурсов, в которой будет создан кластер. |
   | SoftDeletePeriod | *3650:00:00:00* | Время, на протяжении которого данные будут храниться таким образом, чтобы они были доступны для запроса. |
   | HotCachePeriod | *3650:00:00:00* | Время, на протяжении которого данные будут храниться в кэше. |

1. Выполните следующую команду, чтобы просмотреть созданную базу данных:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

Теперь у вас есть кластер и база данных.

## <a name="clean-up-resources"></a>Очистка ресурсов

* Если вы планируете следовать нашим другим статьям, не заполняйте созданные ресурсы.
* Чтобы очистить ресурсы, удалите кластер. При удалении кластера также удаляются все содержащиеся в нем базы данных. Для удаления кластера используйте следующую команду:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Дальнейшие действия

* [Дополнительные команды Az.Kusto](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Прием данных с помощью пакета SDK .NET Standard для Azure Data Explorer (предварительная версия)](./net-sdk-ingest-data.md)