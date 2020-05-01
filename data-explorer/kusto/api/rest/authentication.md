---
title: Проверка подлинности по протоколу HTTPS с обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается проверка подлинности по протоколу HTTPS в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: e0910089d87d6bce6124cb7e4560c2fa7b92b847
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617992"
---
# <a name="authentication-over-https"></a>Проверка подлинности по протоколу HTTPS

При использовании протокола HTTPS служба поддерживает стандартный заголовок HTTP `Authorization` для выполнения проверки подлинности.

Поддерживаются следующие методы проверки подлинности HTTP:

* **Azure Active Directory**с помощью `bearer` метода.

При проверке подлинности с помощью Azure `Authorization` AD заголовок имеет формат:

```txt
Authorization: bearer TOKEN
```

Где `TOKEN` — маркер доступа, получаемый вызывающим объектом путем взаимодействия со службой Azure AD. Маркер имеет следующие свойства:

* Ресурс является URI службы (например, `https://help.kusto.windows.net`).
* Конечная точка службы Azure AD`https://login.microsoftonline.com/TENANT/`

Где `TENANT` — это идентификатор или имя клиента Azure AD. Например, службы, созданные в клиенте Майкрософт, могут использовать `https://login.microsoftonline.com/microsoft.com/`. В качестве альтернативы для проверки подлинности пользователя можно выполнить запрос `https://login.microsoftonline.com/common/`к.

> [!NOTE]
> Конечная точка службы Azure AD изменяется при запуске в национальных облаках.
> Чтобы изменить конечную точку, задайте для переменной `AadAuthorityUri` среды требуемый URI.

Дополнительные информатон см. в статье [Общие сведения о проверке подлинности](../../management/access-control/index.md) и [инструкции по проверке подлинности Azure AD](../../management/access-control/how-to-authenticate-with-aad.md).
