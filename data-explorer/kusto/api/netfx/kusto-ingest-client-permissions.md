---
title: Kusto. принимаемое разрешение на прием — обозреватель данных Azure | Документация Майкрософт
description: В этой статье описываются разрешения на прием ссылок на Kusto. принимаемые в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ec3b91056a4cfc584c0d7168549864b2b50ef5ae
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617924"
---
# <a name="kustoingest-reference---ingestion-permissions"></a>Kusto. принимаемые ссылки — разрешения на прием
В этой статье объясняется, какие разрешения следует настроить в службе для `Native` работы.


## <a name="prerequisites"></a>Предварительные требования
* Сведения о просмотре и изменении параметров авторизации в службах и базах данных Kusto см. в разделе [команды управления Kusto](../../management/security-roles.md) . 

## <a name="references"></a>Ссылки
* Следующие приложения AAD используются в качестве примеров участников в примерах ниже:
    * Тестирование приложения AAD (2a904276-1234-5678-9012-66fc53add60b; microsoft.com)
    * Приложение AAD для внутреннего приема Kusto (76263cdb-1234-5678-9012-545644e9c404; microsoft.com)

## <a name="ingestion-permission-model-for-queued-ingestion"></a>Модель разрешений приема для приема в очереди
Этот режим, определенный в [икустокуеуединжестклиент](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient), ограничивает зависимость клиентского кода от службы Обозреватель данных Azure. Прием выполняется путем отправки сообщения приема Kusto в очередь Azure. Очередь получена из службы обозреватель данных Azure. Ит'сс также называется службой приема.  Принимающий клиент будет создавать любые промежуточные артефакты хранилища, используя ресурсы, выделенные службой обозреватель данных Azure.

На следующей схеме показано взаимодействие клиента приема в очереди с Kusto.<BR>

:::image type="content" source="../images/queued-ingest.jpg" alt-text="приема в очереди":::

### <a name="permissions-on-the-engine-service"></a>Разрешения для службы Engine
Чтобы обеспечить получение данных в таблице `T1` базы данных `DB1`, участник, выполняющий операцию приема, должен иметь авторизацию.
Минимальные требуемые уровни разрешений `Database Ingestor` — `Table Ingestor` и позволяют принимать данные во все существующие таблицы в базе данных или в определенную существующую таблицу.
Если требуется создание таблицы, `Database User` также необходимо назначить более высокую роль доступа.


|Роль |PrincipalType    |принЦипалдисплайнаме
|--------|------------|------------
|`Database Ingestor` |Приложение AAD |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`
|`Table Ingestor` |Приложение AAD |`Test App (app id: 2a904276-1234-5678-9012-66fc53add60b)`

>`Kusto Internal Ingestion AAD App (76263cdb-1234-5678-9012-545644e9c404)`Основное приложение Kusto, которое является внутренним приложением приема, иммутабли сопоставлено с `Cluster Admin` ролью и, таким образом, имеет право принимать данные в любую таблицу. Это происходит в конвейерах приема, управляемых Kusto.

Предоставление требуемых разрешений для `DB1` базы данных `T1` или таблицы приложению `Test App (2a904276-1234-5678-9012-66fc53add60b in AAD tenant microsoft.com)` AAD будет выглядеть следующим образом:
```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
```
