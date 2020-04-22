---
title: Настройка ключей, управляемых клиентами, с помощью портала Azure
description: В этой статье описывается, как настроить шифрование ключей, управляемых клиентом, на данных в Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 13ad30e53ff38e627f44bac96010ae6d21902cee
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497750"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>Настройка ключей, управляемых клиентами, с помощью портала Azure

> [!div class="op_single_selector"]
> * [Портал](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Шаблон Azure Resource Manager](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>Включить шифрование с помощью ключей, управляемых клиентами, на портале Azure

В этой статье показано, как включить шифрование ключей с управлением клиентов с помощью портала Azure. По умолчанию шифрование Azure Data Explorer использует управляемые Корпорацией Майкрософт ключи. Нанастройка кластера Azure Data Explorer для использования ключей, управляемых клиентами, и укажите ключ для ассоциированного с кластером.

1. На [портале Azure](https://portal.azure.com/)перейдите на ресурс [кластера Azure Data Explorer.](create-cluster-database-portal.md#create-a-cluster) 
1. Выберите**Шифрование** **настроек** > в левом стеле портала.
1. В панели **шифрования** выберите **On** для настройки **ключей, управляемых заказчиком.**
1. Нажмите **Выберите ключ**.

    ![Настройка управляемых клиентом ключей](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. В **ключе Select из окна Убежища ключей Azure** выберите существующее **хранилище ключей** из списка выпадающих. Если вы выберете **Создать новое** для создания нового [Убежища ключей,](/azure/key-vault/quick-create-portal#create-a-vault)вы будете направлены на экран Создания **Ключ убежища.**

1. Выберите **ключ**.
1. Выберите **версию**.
1. Нажмите кнопку **Выбрать**.

    ![Выберите ключ из убежища ключей Azure](media/customer-managed-keys-portal/cmk-key-vault.png)

1. В панели **шифрования,** которая теперь содержит ключ, выберите **Сохранить.** Когда создание CMK будет успешным, вы увидите сообщение об успехе в **Уведомлениях.**

    ![Сохранить ключ, управляемый клиентами](media/customer-managed-keys-portal/cmk-encryption-setting.png)

Включив ключевые ключи, управляемые клиентами для кластера Azure Data Explorer, вы создасте систему, назначенную для кластера, если она не существует. Кроме того, вы предоставите необходимые разрешения получить, обернуть Key и unwarpKey в кластер Azure Data Explorer в выбранном Хранилище ключей и получите свойства Key Vault. 

> [!NOTE]
> **Выберите Off,** чтобы удалить ключ, управляемый клиентом, после его создания.

## <a name="next-steps"></a>Следующие шаги

* [Защита кластеров Исследователей данных Azure в Azure](security.md)
* [Защитите свой кластер на портале Azure Data Explorer - Azure,](manage-cluster-security.md) включив шифрование в состоянии покоя.
* [Настройка управляемых клиентами ключей с помощью шаблона управления ресурсами Azure](customer-managed-keys-resource-manager.md)
* [Настройка управляемых клиентами ключей с помощью C #](customer-managed-keys-csharp.md)



