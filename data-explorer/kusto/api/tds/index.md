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
ms.openlocfilehash: cc10fcc725e038d6428d4b794a1f6d368a86a39e
ms.sourcegitcommit: 9e0289945270db517e173aa10024e0027b173b52
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428420"
---
# <a name="ms-tds-t-sql-support"></a>Поддержка MS-TDS T-SQL

Azure Data Explorer поддерживает подмножество протокола связи Microsoft SQL Server (MS-TDS) и языка запросов T-SQL. Microsoft Excel и Microsoft Power BI — лишь некоторые из многих средств, которые можно использовать с Azure Data Explorer. Эти приложения Майкрософт также поддерживают отправку запросов в SQL Server.

> [!NOTE]
> Используйте встроенную проверку подлинности Azure Active Directory (Azure AD) в качестве клиентского средства для отправки запросов к Kusto по протоколу MS-TDS.

## <a name="next-steps"></a>Дальнейшие действия

* [T-SQL.](./t-sql.md) Сведения о реализации языка запросов T-SQL в Kusto. 

* [Отправка запросов KQL через TDS.](./tdskql.md) Сведения о выполнении собственных запросов KQL через конечные точки TDS.

* [Клиенты MS-TDS и Kusto.](./clients.md) Применение Azure Data Explorer из известных клиентов, использующих MS-TDS/T-SQL.

* [Использование Azure Data Explorer в качестве сервера, связанного с SQL Server.](./linkedserver.md) Настройка кластера в качестве сервера, связанного с SQL Server, в локальной среде. 

* [Использование MS-TDS с Azure Active Directory.](./aad.md) Использование Azure AD через протокол TDS для подключения к Azure Data Explorer.

* [Известные проблемы с SQL.](./sqlknownissues.md) Сведения об основных отличиях между реализацией T-SQL в SQL Server и в Azure Data Explorer.
