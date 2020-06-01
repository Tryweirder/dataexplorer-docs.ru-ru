---
title: Настройка ключей, управляемых клиентом, с помощью портал Azure
description: В этой статье описывается, как настроить шифрование ключей, управляемых клиентом, на основе данных в Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: a75329c6aaad4fa31301104f9407ac36d25c3002
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257898"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Настройка ключей, управляемых клиентом, с помощью портал Azure

> [!div class="op_single_selector"]
> * [Портал](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Шаблон Azure Resource Manager](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Включение шифрования с помощью управляемых клиентом ключей в портал Azure

В этой статье показано, как включить шифрование ключей, управляемых клиентом, с помощью портал Azure. По умолчанию в Azure обозреватель данных Encryption используются ключи, управляемые корпорацией Майкрософт. Настройте кластер Azure обозреватель данных для использования ключей, управляемых клиентом, и укажите ключ, связываемый с кластером.

1. В [портал Azure](https://portal.azure.com/)перейдите к ресурсу [кластера Azure обозреватель данных](create-cluster-database-portal.md#create-a-cluster) . 
1. **Settings**  >  На левой панели портала выберите параметры**Шифрование** .
1. На панели **Шифрование** выберите значение **вкл** для параметра **ключ, управляемого клиентом** .
1. Нажмите кнопку **выбрать ключ**.

    ![Настройка управляемых клиентом ключей](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. В окне **Выбор ключа из Azure Key Vault** выберите существующее **хранилище ключей** из раскрывающегося списка. Если выбрать **создать** новый для [создания Key Vault](/azure/key-vault/quick-create-portal#create-a-vault), вы будете направлены на экран **Создание Key Vault** .

1. Выберите **ключ**.
1. Выберите **версию**.
1. Нажмите кнопку **Выбрать**.

    ![Выберите ключ из Azure Key Vault](media/customer-managed-keys-portal/cmk-key-vault.png)

1. В области **Шифрование** , которая теперь содержит ваш ключ, нажмите кнопку **сохранить**. После успешного создания CMK вы увидите сообщение об успешном выполнении в **уведомлениях**.

    ![Сохранить ключ, управляемый клиентом](media/customer-managed-keys-portal/cmk-encryption-setting.png)

Включив управляемые клиентом ключи для кластера Azure обозреватель данных, вы создадите удостоверение, назначенное системой для кластера, если оно не существует. Кроме того, вы будете предоставлять необходимые разрешения GET, wrapKey и Унварпкэй для кластера Azure обозреватель данных на выбранном Key Vault и получать свойства Key Vault. 

> [!NOTE]
> Выберите **Отключить** , чтобы удалить ключ, управляемый клиентом, после его создания.

## <a name="next-steps"></a>Дальнейшие действия

* [Защита кластеров Azure обозреватель данных в Azure](security.md)
* [Защитите свой кластер в Azure обозреватель данных — портал Azure](manage-cluster-security.md) , включив шифрование неактивных компонентов.
* [Настройка ключей, управляемых клиентом, с помощью шаблона Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Настройка ключей, управляемых клиентом, с помощью C #](customer-managed-keys-csharp.md)



