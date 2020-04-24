---
title: Поставщики идентификационных данных и идентификационных данных - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны принципы и поставщики идентификационных данных в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a0638ba0031162dadbb4b9a2815940e66d4dcfb3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522650"
---
# <a name="principals-and-identity-providers"></a>Руководители и поставщики идентификационных данных

Модель авторизации Kusto поддерживает несколько поставщиков идентификационных данных (IdPs) и несколько основных типов.
В этой статье рассматриваются поддерживаемые основные типы и демонстрируется их использование с [командами назначения ролей.](../../management/security-roles.md)

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory (AAD) — это предпочтительный мультитенантный облачный справочник и поставщик идентификационных данных, способный аутентифицировать принципы безопасности или подавать ответные данные с другими поставщиками идентификационных данных, такими как Active Directory от Microsoft.

AAD является предпочтительным методом проверки подлинности на Kusto. Эта служба поддерживает несколько сценариев проверки подлинности:
* **Аутентификация пользователей** (интерактивный вход в систему). Используется для аутентификации субъектов, представляющих людей.
* **Проверка подлинности приложений** (неинтерактивный вход в систему). Используется для проверки подлинности служб и приложений, которые должны запускаться и (или) проходить проверку подлинности без участия пользователя.

>ПРИМЕЧАНИЕ: Active Directory Azure не позволяет аутентифицированию учетных записей служб (которые по определению являются сущностями AD.
Эквивалентa AAD учетной записи службы AD является приложением AAD.

#### <a name="aad-group-principals"></a>Руководители группы AAD
Kusto поддерживает только директоров Группы безопасности (а не группы распределения). Попытка настроить доступ для DG в кластере Kusto приведет к ошибке.

#### <a name="aad-tenants"></a>Арендаторы AAD


>Если арендатор AAD явно не указан, Kusto попытается решить его с UPN (UniversalPrincipalName, например, `johndoe@fabrikam.com`), если это предусмотрено.
Если ваш основной директор не включает информацию о арендаторе (не в форме UPN), вы должны прямо упомянуть об этом, приложив идентификатор или имя арендатора к главному дескриптору.

**Примеры для принципов AAD**

|Арендатор AAD |Тип |Синтаксис |
|-----------|-----|-------|
|Неявно (UPN)  |Пользователь  |`aaduser=`*UserEmailАдрес*
|Явный (ID)   |Пользователь  |`aaduser=`*UserEmailАдрес*`;`*TenantId* или `aaduser=` *ObjectID*`;`*TenantId*
|Явное (имя) |Пользователь  |`aaduser=`*UserEmailАдрес*`;`*TenantName* или `aaduser=` *ObjectID*`;`*TenantName*
|Неявно (UPN)  |Группа |`aadgroup=`*GroupEmailАдрес*
|Явный (ID)   |Группа |`aadgroup=`*GroupObjectId*`;`*TenantId* или`aadgroup=`*GroupDisplayName*`;`*TenantId*
|Явное (имя) |Группа |`aadgroup=`*GroupObjectId*`;`*TenantName* или`aadgroup=`*GroupDisplayName*`;`*TenantName*
|Явный (UPN)  |Приложение   |`aadapp`=*ApplicationDisplayName*`;`*TenantId*
|Явное (имя) |Приложение   |`aadapp=`*ПриложениеId*`;`*TenantName*

```kusto
// No need to specify AAD tenant for UPN, as Kusto performs the resolution by itself
.add database Test users ('aaduser=imikeoein@fabrikam.com') 'Test user (AAD)'

// AAD SG on 'fabrikam.com' tenant
.add database Test users ('aadgroup=SGDisplayName;fabrikam.com') 'Test group @fabrikam.com (AAD)'

// AAD App on 'fabrikam.com' tenant - by tenant name
.add database Test users ('aadapp=4c7e82bd-6adb-46c3-b413-fdd44834c69b;fabrikam.com') 'Test app @fabrikam.com (AAD)'
```

### <a name="microsoft-accounts-msas"></a>Учетные записи Майкрософт (MSA)
Термин "Учетные записи Майкрософт (MSA)" объединяет все управляемые корпорацией Майкрософт учетные записи пользователей, не принадлежащие конкретным организациям, например `hotmail.com`, `live.com` или `outlook.com`.
Kusto поддерживает проверку подлинности для пользователей по MSA (обратите внимание, что этом случае не используются группы безопасности), которые идентифицируются по универсальным именам участников-пользователей.
Если субъект MSA настроен в ресурсе Kusto, Kusto **не будет** пытаться разрешить предоставленное имя участника-пользователя.

**Примеры для директоров MSA**

|URL-адрес  |Тип  |Синтаксис |
|-----|------|-------|
|Live.com |Пользователь  |`msauser=`john.doe@live.com`

```kusto
.add database Test users ('msauser=john.doe@live.com') 'Test user (live.com)'
```

