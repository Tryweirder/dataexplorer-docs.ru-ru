---
title: Защищайте кластер в Azure Data Explorer
description: В этой статье описывается, как обезопасить кластер в Azure Data Explorer на портале Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496203"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Защищайте кластер в Azure Data Explorer - портал Azure

[Шифрование Azure Disk](/azure/security/azure-security-disk-encryption-overview) помогает защитить и защитить ваши данные в соответствии с вашими обязательствами по обеспечению безопасности и соответствия требованиям организации. Он обеспечивает громкое шифрование для ОС и дисков данных ваших кластерных виртуальных машин. Он также интегрируется с [Azure Key Vault](/azure/key-vault/), который позволяет нам управлять и управлять ключами шифрования диска и секретами, а также гарантировать, что все данные на дисках VM зашифрованы. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Включить шифрование в покое на портале Azure
  
Настройки безопасности кластера позволяют включить шифрование диска в кластере. Включение [шифрования в покое](/azure/security/fundamentals/encryption-atrest) в кластере обеспечивает защиту данных для хранимых данных (в состоянии покоя). 

1. На портале Azure перейдите на ресурс кластера Azure Data Explorer. Под заголовком **«Настройки»** выберите **Security.** 

    ![Включите шифрование в состоянии покоя](media/manage-cluster-security/security-encryption-at-rest.png)

1. В окне **безопасности** выберите **On** для настройки **безопасности шифрования диска.** 

1. Щелкните **Сохранить**.
 
> [!NOTE]
> Выберите **Off,** чтобы отключить шифрование после того, как оно было включено.

## <a name="next-steps"></a>Следующие шаги

[Проверка работоспособности кластера](/azure/data-explorer/check-cluster-health)
