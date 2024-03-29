---
title: Настройка ключей, управляемых клиентом, с помощью Azure CLI
description: В этой статье описывается, как настроить шифрование ключей, управляемых клиентом, на основе данных в Azure обозреватель данных с помощью Azure CLI.
author: orspod
ms.author: orspodek
ms.reviewer: astauben
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/01/2020
ms.openlocfilehash: c343ac1177e439f75116a3da77def5862d5d2a7f
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872137"
---
# <a name="configure-customer-managed-keys-using-azure-cli"></a>Настройка ключей, управляемых клиентом, с помощью Azure CLI

> [!div class="op_single_selector"]
> * [Портал](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Шаблон Azure Resource Manager](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-using-azure-cli"></a>Включение шифрования с использованием управляемых клиентом ключей с помощью Azure CLI
В этой статье показано, как включить шифрование ключей, управляемых клиентом, с помощью клиента Azure CLI. По умолчанию в Azure обозреватель данных Encryption используются ключи, управляемые корпорацией Майкрософт. Настройте кластер Azure обозреватель данных для использования ключей, управляемых клиентом, и укажите ключ, связываемый с кластером.

1. Выполните следующую команду, чтобы войти в Azure:

    ```azurecli-interactive
    az login
    ```

1. Задайте подписку, в которой зарегистрирован кластер. Замените *мязуресуб* именем подписки Azure, которую вы хотите использовать.

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

1. Выполните следующую команду, чтобы задать новый ключ.
    ```azurecli-interactive
    az kusto cluster update --cluster-name "mytestcluster" --resource-group "mytestrg" --key-vault-properties key-name="<key-name>" key-version="<key-version>" key-vault-uri="<key-vault-uri>"
    ```
1. Выполните следующую команду и проверьте свойство "Кэйваултпропертиес", чтобы убедиться, что кластер успешно обновлен.

    ```azurecli-interactive
    az kusto cluster show --cluster-name "mytestcluster" --resource-group "mytestrg"
    ```

