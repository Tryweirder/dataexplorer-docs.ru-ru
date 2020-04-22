---
title: Строки подключения (Azure Data Explorer) | Документация Майкрософт
description: В этой статье описываются строки подключения в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 53c3caecc373a646162016fc1717ce1b0b0b85d1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490078"
---
# <a name="connection-strings"></a>Строки соединения

Строки подключения широко используются в управляющих командах Kusto, в API Kusto и иногда даже в запросах.
Строки подключения предоставляют обобщенный метод описания способов поиска внешних по отношению к Kusto ресурсов и взаимодействия с ними, например с большими двоичными объектами в службе хранилища BLOB-объектов Azure и с Базами данных SQL Azure.

Есть несколько форматов строк подключения:

* [Строки подключения Kusto](./kusto.md) описывают взаимодействие с конечной точкой службы Kusto.
  Строки подключения Kusto основаны на [модели строк подключения ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax).
* [Строки подключения к хранилищу](./storage.md) описывают, как указывать Kusto во внешней службе хранилища, например в хранилище BLOB-объектов Azure или в Azure Data Lake Storage.
* Строки подключения SQL используются [подключаемым модулем sql_request](../../query/sqlrequestplugin.md) для Kusto, чтобы создавать запросы к службе Базы данных Azure, и [командой экспорта в SQL](../../management/data-export/export-data-to-sql.md).  
  Эти строки подключения соответствуют спецификации [строк подключения SqlClient](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings).