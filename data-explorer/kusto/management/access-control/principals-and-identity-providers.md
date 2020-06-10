---
title: Субъекты и поставщики удостоверений — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются субъекты и поставщики удостоверений в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4e34c724799cffe38db93869e96fcfae83a92b55
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664999"
---
# <a name="principals-and-identity-providers"></a>Субъекты и поставщики удостоверений

Модель авторизации Kusto поддерживает несколько поставщиков удостоверений (поставщиков удостоверений) и несколько типов субъектов.
В этой статье рассматриваются поддерживаемые типы участников и демонстрируется их использование с [командами назначения ролей](../../management/security-roles.md).

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory (AAD) — предпочтительная служба облачного каталога и поставщик удостоверений Azure, способная выполнять проверку подлинности субъектов безопасности или федерацию с другими поставщиками удостоверений, например Active Directory Майкрософт.

AAD является предпочтительным методом проверки подлинности в Kusto. Эта служба поддерживает несколько сценариев проверки подлинности:
* **Аутентификация пользователей** (интерактивный вход в систему). Используется для аутентификации субъектов, представляющих людей.
* **Проверка подлинности приложений** (неинтерактивный вход в систему). Используется для проверки подлинности служб и приложений, которые должны запускаться и (или) проходить проверку подлинности без участия пользователя.

> [!NOTE]
> Azure Active Directory не разрешает проверку подлинности учетных записей служб (которые определяются локальными сущностями AD).
Эквивалентом AAD учетной записи службы AD является приложение AAD.

#### <a name="aad-group-principals"></a>Участники группы AAD
Kusto поддерживает только субъекты группы безопасности (а не группы распространения). Попытка настроить доступ к группе рассылки в кластере Kusto приведет к ошибке.

#### <a name="aad-tenants"></a>Клиенты AAD

Если клиент AAD не указан явно, Kusto попытается разрешить его из имени участника-пользователя (УниверсалпринЦипалнаме, например, `johndoe@fabrikam.com` ), если оно предоставлено. Если участник не содержит сведений о клиенте (не в форме UPN), необходимо явно указать его, добавив идентификатор клиента или имя в дескриптор участника.

**Примеры для субъектов AAD**

|Клиент AAD |Type |Синтаксис |
|-----------|-----|-------|
|Implicit (UPN)  |Пользователь  |`aaduser=`*усеремаиладдресс*
|Explicit (идентификатор)   |Пользователь  |`aaduser=`*Усеремаиладдресс* `;` *Tenantid* или `aaduser=` *ObjectID* `;` *tenantid*
|Explicit (имя) |Пользователь  |`aaduser=`*Усеремаиладдресс* `;` *TenantName* или `aaduser=` *ObjectID* `;` *TenantName*
|Implicit (UPN)  |Группа |`aadgroup=`*граупемаиладдресс*
|Explicit (идентификатор)   |Группа |`aadgroup=`*Граупобжектид* `;` *Tenantid* или `aadgroup=` *GroupDisplayName* `;` *tenantid*
|Explicit (имя) |Группа |`aadgroup=`*Граупобжектид* `;` *TenantName* или `aadgroup=` *GroupDisplayName* `;` *TenantName*
|Explicit (UPN)  |Приложение   |`aadapp`=*ApplicationDisplayName* `;` *ИД* клиента
|Explicit (имя) |Приложение   |`aadapp=`*ApplicationId* `;` *TenantName*

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

**Примеры для участников MSA**

|IdP  |Type  |Синтаксис |
|-----|------|-------|
|Live.com |Пользователь  |`msauser=`john.doe@live.com`

```kusto
.add database Test users ('msauser=john.doe@live.com') 'Test user (live.com)'
```

