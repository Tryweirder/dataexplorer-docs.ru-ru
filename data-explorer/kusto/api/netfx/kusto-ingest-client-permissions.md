---
title: Kusto.Ingest Справка - Разрешения на проглатывание - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается ссылка Kusto.Ingest - Разрешения на проглатку в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 198d562f880b74f6df4c6318f72213ee865f8f28
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502896"
---
# <a name="kustoingest-reference---ingestion-permissions"></a>Kusto.Ingest Справка - Разрешение на проглатку
В этой статье объясняется, какие разрешения должны быть `Native` настроены на службу для того, чтобы проглатывание работало.



## <a name="prerequisites"></a>Предварительные требования
* В этой статье говорится о том, как использовать [команды управления Kusto](../../management/security-roles.md) для просмотра и изменения параметров авторизации в службах и базах данных Kusto
* Следующие приложения AAD используются в качестве основ выборки в приведенных ниже примерах:
    * Тест AAD App (2a904276-1234-5678-9012-66fc53add60b;microsoft.com)
    * Kusto Внутреннее заглатывание AAD App (76263cdb-1234-5678-9012-5456444e9c404;microsoft.com)

## <a name="ingestion-permission-model-for-queued-ingestion"></a>Модель разрешения на проглатывание для проглатывания в очереди
Этот режим, определяемый в [IKusto'u'uingingestClient,](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)ограничивает зависимость клиентского кода от службы Kusto. Загласка выполняется путем размещения сообщения о проглатывании Кусто в очередь Azure, которая, в свою очередь, приобретается у Kusto Data Management (также известная как компания. Проглатывание) обслуживание. Любые промежуточные артефакты хранения будут создаваться клиентом, использующим ресурсы, выделенные службой управления данными Kusto.<BR>

На следующей диаграмме описывается взаимодействие клиента в очереди с Kusto:<BR>

![альт текст](../images/queued-ingest.jpg "в очереди-ingest")

### <a name="permissions-on-the-engine-service"></a>Разрешения на обслуживание двигателя
Для того, чтобы претендовать на `T1` получение `DB1` данных в таблицу базы данных, для этого необходимо авторизовать основную операцию по поглощению.
Минимальные требуемые уровни разрешения, `Database Ingestor` которые `Table Ingestor` могут глотать данные во все существующие таблицы в базе данных или в конкретную существующую таблицу, соответственно.
Если требуется создание `Database User` таблицы или должна быть назначена более высокая роль доступа.


|Роль |PrincipalType    |PrincipalDisplayName
|--------|------------|------------
|База данных - Ingestor |Применение AAD |Тест-приложение (идентификатор приложения: 2a904276-1234-5678-9012-66fc53add60b)
|Таблица Ingestor |Применение AAD |Тест-приложение (идентификатор приложения: 2a904276-1234-5678-9012-66fc53add60b)

>`Kusto Internal Ingestion AAD App (76263cdb-1234-5678-9012-545644e9c404)`principal (Kusto internal Ingestion App) непреложно `Cluster Admin` отображается на роли и, таким образом, уполномочен глотать данные в любую таблицу (это то, что происходит на трубопроводах, управляемых Кусто).

Предоставление необходимых разрешений в базе данных `DB1` или таблице `T1` AAD App `Test App (2a904276-1234-5678-9012-66fc53add60b in AAD tenant microsoft.com)` будет выглядеть следующим образом:
```kusto
.add database DB1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
.add table T1 ingestors ('aadapp=2a904276-1234-5678-9012-66fc53add60b;microsoft.com') 'Test AAD App'
```

