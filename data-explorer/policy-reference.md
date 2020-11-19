---
title: Встроенные определения политик для Azure обозреватель данных
description: Выводит список встроенных определений политик Azure для обозреватель данных Azure. Эти встроенные определения политик предоставляют популярные подходы к управлению ресурсами Azure.
ms.date: 11/17/2020
ms.topic: reference
author: orspod
ms.author: orspodek
ms.service: data-explorer
ms.custom: subject-policy-reference
ms.openlocfilehash: 9f2e98a1be83be659c8f7b85283c868d96f999eb
ms.sourcegitcommit: 0820454feb02ae489f3a86b688690422ae29d788
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94937930"
---
# <a name="azure-policy-built-in-definitions-for-azure-data-explorer"></a>Встроенные определения политики Azure для Azure обозреватель данных

Эта страница является индексом определений встроенных политик политики [Azure](/azure/governance/policy/overview) для обозреватель данных Azure. Дополнительные встроенные компоненты Политики Azure для других служб см. в статье [Встроенные определения Политики Azure](/azure/governance/policy/samples/built-in-policies).

Имя каждого встроенного определения политики связано с определением политики на портале Azure. Перейдите по ссылке в столбце **Версия**, чтобы просмотреть исходный код в [репозитории GitHub для службы "Политика Azure"](https://github.com/Azure/azure-policy).

## <a name="azure-data-explorer"></a>Azure Data Explorer

|Имя<br /><sub>(портал Azure)</sub> |Описание |Действие |Версия<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Шифрование неактивных обозреватель данных в Azure должно использовать управляемый клиентом ключ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Включение шифрования неактивных ключей с помощью управляемого клиентом ключа в кластере Azure обозреватель данных обеспечивает дополнительный контроль над ключом, используемым шифрованием неактивных элементов. Эта функция часто применяется к клиентам с особыми требованиями к соответствию, и для управления ключами требуется Key Vault. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[Шифрование дисков должно быть включено в Azure обозреватель данных](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |Включение шифрования дисков помогает защитить данные в соответствии с вашими требованиями к безопасности и соблюдению требований Организации. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[В Azure обозреватель данных должно быть включено двойное шифрование](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |Включение двойного шифрования помогает защитить данные и обеспечить их защиту в соответствии с вашими требованиями к безопасности и соблюдению требований Организации. Если включено двойное шифрование, данные в учетной записи хранения шифруются дважды, один раз на уровне службы и один раз на уровне инфраструктуры, используя два разных алгоритма шифрования и два разных ключа. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[Внедрение виртуальной сети должно быть включено для Azure обозреватель данных](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |Обеспечьте безопасность периметра сети с помощью внедрения виртуальной сети, который позволяет применять правила группы безопасности сети, подключать локальные ресурсы и защищать источники подключения к данным с помощью конечных точек служб. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со встроенными инициативами в [репозитории GitHub для Политики Azure](https://github.com/Azure/azure-policy).
- Изучите статью о [структуре определения Политики Azure](/azure/governance/policy/concepts/definition-structure).
- Изучите [сведения о действии политик](/azure/governance/policy/concepts/effects).
