---
title: Поддержка MS-TDS T-SQL в Azure Data Explorer
description: В этой статье описывается поддержка MS-TDS T-SQL в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/06/2019
ms.openlocfilehash: a128db995c78c0583bc7c7712c06292a2f6598d1
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550543"
---
# <a name="ms-tds-t-sql-support"></a>Поддержка MS-TDS T-SQL

Azure Data Explorer (Kusto) поддерживает подмножество протокола связи Microsoft SQL Server (MS-TDS) и языка запросов T-SQL. Microsoft Excel и Microsoft Power BI — лишь некоторые из многих средств, которые можно использовать с Azure Data Explorer (Kusto). Эти приложения Майкрософт также поддерживают отправку запросов в SQL Server.

> [!NOTE]
> Используйте встроенную проверку подлинности Azure Active Directory (Azure AD) в качестве клиентского средства для отправки запросов к Kusto по протоколу MS-TDS.

## <a name="next-steps"></a>Дальнейшие действия

* [T-SQL.](./t-sql.md) Сведения о реализации языка запросов T-SQL в Kusto. 

* [Отправка запросов KQL через TDS.](./tdskql.md) Сведения о выполнении собственных запросов KQL через конечные точки TDS.

* [Клиенты MS-TDS и Kusto.](./clients.md) Применение Azure Data Explorer из известных клиентов, использующих MS-TDS/T-SQL.

* [Использование Azure Data Explorer (Kusto) в качестве сервера, связанного с SQL Server.](./linkedserver.md) Настройка кластера в качестве сервера в локальной среде, связанного с SQL Server. 

* [Использование MS-TDS с Azure Active Directory.](./aad.md) Использование Azure AD через протокол TDS для подключения к Azure Data Explorer.

* [Известные проблемы с SQL.](./sqlknownissues.md) Сведения об основных отличиях между реализацией T-SQL в SQL Server и в Azure Data Explorer.
