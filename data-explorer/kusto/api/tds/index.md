---
title: MS-TDS (поддержка T-SQL) в Azure Data Explorer | Документация Майкрософт
description: В этой статье описывается MS-TDS (поддержка T-SQL) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/06/2019
ms.openlocfilehash: 8aaea26b6c4e8a7f76c4129faeb681791f25ae7e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490554"
---
# <a name="ms-tds-t-sql-support"></a>MS-TDS (поддержка T-SQL)

Kusto поддерживает подмножество возможностей протокола связи MS-TDS (который используется в Microsoft SQL Server) и подмножество языка запросов T-SQL, чтобы с Kusto могли работать существующие средства, которые умеют выполнять запросы к SQL Server. Поддерживаются такие клиенты, как Microsoft Excel, Microsoft Power BI и многие другие.

Обратите внимание, что для запросов к Kusto из клиентского средства через MS-TDS клиент должен поддерживать встроенную проверку подлинности Azure Active Directory.

Дополнительные сведения о реализации языка запросов T-SQL в Kusto см. в [этой статье](./t-sql.md). 

Примеры использования Kusto из некоторых популярных клиентов с помощью MS-TDS/T-SQL см. [здесь](./clients.md).

Настройка кластера Kusto в качестве сервера в локальной среде, связанного с SQL Server, описана [здесь](./linkedserver.md).

Дополнительные сведения об использовании AAD через TDS для подключения к Kusto см. в [этой статье](./aad.md).

Сведения о запуске собственных запросов KQL через конечную точку TDS см. [здесь](./tdskql.md). 

И наконец, [здесь](./sqlknownissues.md) вы найдете основные сведения об отличиях между реализацией T-SQL в SQL Server и в Kusto.