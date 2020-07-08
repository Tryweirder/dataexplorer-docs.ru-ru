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
ms.openlocfilehash: 9cea718db64c3da998df8b832d886ebd87d0f241
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86058768"
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

> [!NOTE]
> В некоторых строках подключения могут содержаться ссылки на субъекты безопасности. Сведения об указании субъектов безопасности в строках подключения см. в статье [Субъекты и поставщики удостоверений](../../management/access-control/principals-and-identity-providers.md).
