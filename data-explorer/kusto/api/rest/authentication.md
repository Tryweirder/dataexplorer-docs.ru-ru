---
title: Аутентификация по HTTPS - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается аутентификация по сравнению с HTTPS в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 4b6fbf5bb34dc3ff52938c7042778a7e49fd5faf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503049"
---
# <a name="authentication-over-https"></a>Аутентификация по HTTPS

При использовании HTTPS служба `Authorization` поддерживает стандартный заголовок HTTP для выполнения аутентификации.

Поддерживаемые методы проверки подлинности HTTP:

* **Активный каталог Azure**с `bearer` помощью метода.

При проверке подлинности с помощью `Authorization` Active Directory Azure заголовок имеет формат:

```txt
Authorization: bearer TOKEN
```

Где `TOKEN` же контокен доступа, который абонент приобретает, связавшись с службой Active Directory Azure со следующими свойствами:

* Ресурсом является услуга URI (например, `https://help.kusto.windows.net`).
* Конечная точка службы Active `https://login.microsoftonline.com/TENANT/`Directory — это точка .

Где `TENANT` находится идентификатор или имя арендатора Active Directory Azure. Например, службы, созданные `https://login.microsoftonline.com/microsoft.com/`под арендатором Майкрософт, могут использоваться. Кроме того, только для проверки подлинности `https://login.microsoftonline.com/common/` пользователя, вместо этого можно сделать запрос.

> [!NOTE]
> Конечная точка службы Active Directory изменяется при запуске в национальных облаках.
> Чтобы изменить конечную точку, которая будет `AadAuthorityUri` использоваться, установите переменную среды в требуемый URI.

Для получения дополнительной информации смотрите [обзор аутентификации](../../management/access-control/index.md) и [руководство по аутентификации Active Directory Azure.](../../management/access-control/how-to-authenticate-with-aad.md)