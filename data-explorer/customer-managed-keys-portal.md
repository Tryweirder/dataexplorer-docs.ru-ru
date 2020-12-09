---
title: Настройка ключей, управляемых клиентом, с помощью портал Azure
description: Узнайте, как настроить ключи, управляемые клиентом, для шифрования данных Azure обозреватель данных с помощью портал Azure.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 0f77782b5174683d091685064afa7debff7ae777
ms.sourcegitcommit: 202357f866801aafd92e3e29a84bb312e17aebc7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96933949"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Настройка ключей, управляемых клиентом, с помощью портал Azure

> [!div class="op_single_selector"]
> * [Портал](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Шаблон Azure Resource Manager](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Включение шифрования с помощью управляемых клиентом ключей в портал Azure

В этой статье показано, как включить шифрование ключей, управляемых клиентом, с помощью портал Azure. По умолчанию в Azure обозреватель данных Encryption используются ключи, управляемые корпорацией Майкрософт. Настройте кластер Azure обозреватель данных для использования ключей, управляемых клиентом, и укажите ключ, связываемый с кластером.

1. В [портал Azure](https://portal.azure.com/)перейдите к ресурсу [кластера Azure обозреватель данных](create-cluster-database-portal.md#create-a-cluster) .
1.   >  На левой панели портала выберите параметры **Шифрование** .
1. На панели **Шифрование** выберите значение **вкл** для параметра **ключ, управляемого клиентом** .
1. Нажмите кнопку **выбрать ключ**.

    :::image type="content" source="media/customer-managed-keys-portal/customer-managed-key-encryption-setting.png" alt-text="Настройка управляемых клиентом ключей":::

1. В окне **Выбор ключа из Azure Key Vault** выберите существующее **хранилище ключей** из раскрывающегося списка. Если выбрать **создать** новый для [создания Key Vault](/azure/key-vault/quick-create-portal#create-a-vault), вы будете направлены на экран **Создание Key Vault** .

1. Выберите **ключ**.
1. Выберите **версию**.
1. Нажмите кнопку **Выбрать**.

    :::image type="content" source="media/customer-managed-keys-portal/customer-managed-key-key-vault.png" alt-text="Выберите ключ из Azure Key Vault":::

1. В разделе **тип удостоверения** выберите " **назначено системой** " или " **назначено пользователем**".
1. Если выбрано значение **назначено пользователем**, выберите в раскрывающемся списке удостоверение, назначенное пользователем.

    :::image type="content" source="media/customer-managed-keys-portal/customer-managed-key-select-user-type.png" alt-text="Выбор типа управляемого удостоверения":::

1. В области **Шифрование** , которая теперь содержит ваш ключ, нажмите кнопку **сохранить**. После успешного создания CMK вы увидите сообщение об успешном выполнении в **уведомлениях**.

    :::image type="content" source="media/customer-managed-keys-portal/customer-managed-key-before-save.png" alt-text="Сохранить ключ, управляемый клиентом":::

Если при включении ключей, управляемых клиентом для кластера Azure обозреватель данных, вы выбрали удостоверение, назначенное системой, вы создадите для кластера удостоверение, назначенное системой, если оно не существует. Кроме того, вы будете предоставлять необходимые разрешения GET, wrapKey и Унварпкэй для кластера Azure обозреватель данных на выбранном Key Vault и получать свойства Key Vault.

> [!NOTE]
> Выберите **Отключить** , чтобы удалить ключ, управляемый клиентом, после его создания.

## <a name="next-steps"></a>Дальнейшие действия

* [Защита кластеров Azure обозреватель данных в Azure](security.md)
* [Защитите свой кластер с помощью шифрования дисков в Azure обозреватель данных — портал Azure](cluster-disk-encryption.md) , включив шифрование неактивных компонентов.
* [Настройка ключей, управляемых клиентом, с помощью шаблона Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Настройка ключей, управляемых клиентом, с помощью C #](customer-managed-keys-csharp.md)
