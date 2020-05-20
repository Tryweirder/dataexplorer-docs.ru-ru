---
title: Kusto. прием разрешений — Azure обозреватель данных
description: В этой статье описываются разрешения Kusto. прием приема в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2e88ba9af0b9563274e15eff8d1c1f6e997fb45c
ms.sourcegitcommit: e66c5f4b833b4f6269bb7bfa5695519fcb11d9fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83630013"
---
# <a name="kustoingest---ingestion-permissions"></a>Разрешения на прием Kusto. приема 

В этой статье объясняется, какие разрешения следует настроить в службе для `Native` работы.

## <a name="prerequisites"></a>Предварительные условия
 
* Сведения о просмотре и изменении параметров авторизации в службах и базах данных Kusto см. в разделе [команды управления Kusto](../../management/security-roles.md).

* Azure Active Directory приложения (Azure AD), используемые в качестве примеров участников, в следующих примерах:
    * Тестовая Azure AD App (2a904276-1234-5678-9012-66fc53add60b; microsoft.com)
    * Azure AD App внутреннего приема Kusto (76263cdb-1234-5678-9012-545644e9c404; microsoft.com)
 
## <a name="ingestion-permission-mode-for-queued-ingestion"></a>Режим разрешения приема для приема в очереди

Режим разрешения приема определяется в [икустокуеуединжестклиент](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient). Этот режим ограничивает зависимость клиентского кода от службы обозреватель данных Azure. Прием выполняется путем отправки сообщения приема Kusto в очередь Azure. Очередь, также известная как служба приема, поступила из службы обозреватель данных Azure. Принимающий клиент будет создавать промежуточные артефакты хранилища, используя ресурсы, выделенные службой обозреватель данных Azure.

На схеме показано взаимодействие клиента приема в очереди с Kusto.

:::image type="content" source="../images/kusto-ingest-client-permissions/queued-ingest.png" alt-text="Прием с постановкой в очередь":::

### <a name="permissions-on-the-engine-service"></a>Разрешения для службы Engine

Чтобы обеспечить получение данных в таблице `T1` базы данных `DB1` , участник, выполняющий операцию приема, должен иметь авторизацию.
Минимальные требуемые уровни разрешений — `Database Ingestor` и `Table Ingestor` позволяют принимать данные во все существующие таблицы в базе данных или в определенную существующую таблицу.
Если требуется создание таблицы, `Database User` также необходимо назначить более высокую роль доступа.


|Роль                 |PrincipalType        |принЦипалдисплайнаме
|---------------------|---------------------|------------
|`Database Ingestor`  |Приложение Azure AD |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`
|`Table Ingestor`     |Приложение Azure AD |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`

>`Kusto Internal Ingestion Azure AD App (76263cdb-1234-5678-9012-545644e9c404)`Основное приложение Kusto, которое является внутренним приложением приема, иммутабли сопоставлено с `Cluster Admin` ролью. Таким же авторизацией можно принимать данные в любую таблицу. Это происходит в конвейерах приема, управляемых Kusto.

Предоставление требуемых разрешений для базы данных `DB1` или таблицы `T1` в Azure AD App `Test App (2a904276-1234-5678-9012-66fc53add60b in Azure AD tenant microsoft.com)` будет выглядеть следующим образом:

```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test Azure AD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test Azure AD App'
```
 