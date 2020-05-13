---
title: Защита кластера в Azure обозреватель данных
description: В этой статье описывается, как защитить кластер в Azure обозреватель данных в портал Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 3daf9f5af46338a450a1f1a4e9c80a6c83841343
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374016"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Защита кластера в Azure обозреватель данных — портал Azure

[Шифрование дисков Azure](/azure/security/azure-security-disk-encryption-overview) помогает защитить данные в соответствии с обязательствами по обеспечению безопасности и соответствия требованиям Организации. Он обеспечивает шифрование томов для дисков операционной системы и данных виртуальных машин кластера. Кроме того, она интегрируется с [Azure Key Vault](/azure/key-vault/), что позволяет управлять ключами и секретами шифрования диска и управлять ими, а также обеспечивать шифрование всех данных на дисках виртуальной машины. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Включение шифрования неактивных компонентов в портал Azure
  
Параметры безопасности кластера позволяют включить шифрование дисков в кластере. Включение [шифрования неактивных](/azure/security/fundamentals/encryption-atrest) данных в кластере обеспечивает защиту хранящихся данных (неактивных). 

1. В портал Azure перейдите к ресурсу кластера Azure обозреватель данных. Под заголовком **Параметры** выберите **Безопасность**. 

    ![Включение шифрования при неактивных параметрах](media/manage-cluster-security/security-encryption-at-rest.png)

1. В окне **Безопасность** выберите **вкл** . для параметра безопасность **шифрования диска** . 

1. Нажмите кнопку **Сохранить**.
 
> [!NOTE]
> Выберите **Отключить** , чтобы отключить шифрование после его включения.

## <a name="next-steps"></a>Следующие шаги

[Проверка работоспособности кластера](check-cluster-health.md)
