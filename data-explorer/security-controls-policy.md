---
title: Элементы управления соответствием в службе "Политика Azure" для Azure Data Explorer
description: Содержит список элементов управления соответствием из службы "Политика Azure" для Azure Data Explorer. Эти встроенные определения политик предоставляют популярные подходы к управлению соответствием ресурсов Azure.
ms.date: 01/27/2021
ms.topic: sample
author: orspod
ms.author: orspodek
ms.service: data-explorer
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: 3d27a642afd13e25fb593a3ca79bf63e78aa408f
ms.sourcegitcommit: c65080267f32f23205134c4d685908db58fb87c0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99099171"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-data-explorer"></a>Элементы управления соответствием в службе "Политика Azure" для Azure Data Explorer

Статья [Соответствие нормативным требованиям в политике Azure](/azure/governance/policy/concepts/regulatory-compliance) содержит созданные и управляемые корпорацией Майкрософт определения инициатив, называемые _встроенными_, для **доменов соответствия** и **элементов управления безопасностью**, которые связаны с различными стандартами соответствия. На этой странице перечислены **домены соответствия** и **элементы управления безопасностью** для Azure Data Explorer. Вы можете назначить эти встроенные элементы для индивидуального **управления безопасностью**, чтобы обеспечить соответствие ресурсов Azure какому-либо определенному стандарту.

Заголовок каждого встроенного определения политики связан с определением политики на портале Azure. Перейдите по ссылке в столбце **Версия политики**, чтобы просмотреть исходный код в [репозитории GitHub для службы "Политика Azure"](https://github.com/Azure/azure-policy).

> [!IMPORTANT]
> Каждый элемент управления ниже связан с одним или несколькими определениями [Политики Azure](/azure/governance/policy/overview). Такие политики помогут вам в [оценке соответствия](/azure/governance/policy/how-to/get-compliance-data) с помощью элементов управления, но часто полное или точное соответствие между элементом управления и одной или несколькими политиками отсутствует. Поэтому состояние **Соответствует** в Политике Azure применимо только к самим политикам и не означает полное соответствие всем требованиям элемента управления. Кроме того, стандарт соответствия включает элементы управления, на которые сейчас не распространяются определения Политики Azure. Следовательно, сведения о соответствии в Политике Azure — это только частичное представление общего состояния соответствия. Связи между элементами управления и определениями соответствия нормативным требованиям Политики Azure для этих стандартов соответствия со временем могут меняться.

## <a name="cmmc-level-3"></a>CMMC уровня 3

Дополнительные сведения о том, в какой мере доступные встроенные компоненты Политики Azure для всех служб Azure отвечают этому стандарту соответствия, см. в статье [Сведения о встроенной инициативе по соответствию требованиям стандарта CMMC уровня 3](/azure/governance/policy/samples/cmmc-l3). Дополнительные сведения об этом стандарте соответствия см. в документе о [Cybersecurity Maturity Model Certification](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Домен |Идентификатор элемента управления |Заголовок элемента управления |Политика<br /><sub>(портал Azure)</sub> |Версия политики<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Защита системы и средств передачи данных |SC.3.177 |Применение шифрование, проверенного FIPS, при использовании для защиты конфиденциальности контролируемой несекретной информации. |[Во время шифрования неактивных данных в Azure Data Explorer должен использоваться управляемый клиентом ключ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|Защита системы и средств передачи данных |SC.3.177 |Применение шифрование, проверенного FIPS, при использовании для защиты конфиденциальности контролируемой несекретной информации. |[В Azure Data Explorer должно быть включено шифрование дисков](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|Защита системы и средств передачи данных |SC.3.177 |Применение шифрование, проверенного FIPS, при использовании для защиты конфиденциальности контролируемой несекретной информации. |[В Azure Data Explorer должно быть включено двойное шифрование](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|Защита системы и средств передачи данных |SC.3.191 |Защита конфиденциальности неактивной контролируемой несекретной информации |[В Azure Data Explorer должно быть включено шифрование дисков](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|Защита системы и средств передачи данных |SC.3.191 |Защита конфиденциальности неактивной контролируемой несекретной информации |[В Azure Data Explorer должно быть включено двойное шифрование](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [соответствии требованиям Политики Azure](/azure/governance/policy/concepts/regulatory-compliance).
- Ознакомьтесь со встроенными инициативами в [репозитории GitHub для Политики Azure](https://github.com/Azure/azure-policy).
