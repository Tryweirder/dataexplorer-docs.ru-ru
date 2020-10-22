---
title: Общие сведения об API Azure Data Explorer — служба Azure Data Explorer
description: В этой статье приводится описание API службы Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: vladikb
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2020
ms.openlocfilehash: b352ed44e7beb487588cf94f37d280eb6b51cdf9
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337471"
---
# <a name="azure-data-explorer-api-overview"></a>Описание API Azure Data Explorer

Служба Azure Data Explorer поддерживает следующие конечные точки для взаимодействия:

1. Конечная точка [REST API](#rest-api), с помощью которой можно запрашивать данные и управлять ими в Azure Data Explorer.
   Эта конечная точка поддерживает [язык запросов Kusto](../query/index.md) для запросов и [команд управления](../management/index.md).
1. Конечная точка [MS-TDS](#ms-tds), которая реализует ряд возможностей протокола Microsoft TDS (поток табличных данных), используемых в продуктах Microsoft SQL Server.
   Эта конечная точка применяется для средств, которые могут взаимодействовать с конечной точкой SQL Server для отправки запросов.
1. Конечная точка [Azure Resource Manager (ARM)](/azure/role-based-access-control/resource-provider-operations#microsoftkusto), которая является стандартным решением для служб Azure. Она используется для управления ресурсами, такими как кластеры Azure Data Explorer.

## <a name="rest-api"></a>REST API

Основным методом взаимодействия со службой Azure Data Explorer является интерфейс REST API этой службы. Благодаря этой полностью документированной конечной точке вызывающие объекты могут выполнять следующее:

* запрашивать данные;
* запросы к метаданным и их изменение;
* Прием данных
* запрос состояния работоспособности службы;
* Управление ресурсами

Разные службы Azure Data Explorer взаимодействуют между собой через один и тот же общедоступный интерфейс REST API.

Можно также использовать различные [клиентские библиотеки](client-libraries.md), которые позволяют работать со службой без применения протокола REST API.

## <a name="ms-tds"></a>MS-TDS

Azure Data Explorer также поддерживает протокол связи Microsoft SQL Server (MS-TDS) и ограниченно поддерживает выполнение запросов T-SQL. Этот протокол позволяет выполнять запросы к Azure Data Explorer с помощью знакомого синтаксиса запросов (T-SQL) и клиентских средств для работы с базами данных (например, LINQPad, sqlcmd, Tableau, Excel и Power BI).

Дополнительные сведения см. в статье [Поддержка MS-TDS T-SQL](tds/index.md).

## <a name="client-libraries"></a>Клиентские библиотеки 

Azure Data Explorer предоставляет несколько клиентских библиотек, которые используют перечисленные выше конечные точки, для упрощения программного доступа.

* Пакет SDK для .NET
* Пакет SDK для Python
* R
* Пакет SDK для Java
* Пакет SDK для Node
* Пакет SDK для GO
* PowerShell

### <a name="net-framework-libraries"></a>Библиотеки платформы .NET

Библиотеки .NET Framework — это рекомендуемый способ вызова функций Azure Data Explorer программными средствами.
Предоставляется несколько библиотек:

* [Kusto.Data (клиентская библиотека Kusto).](./netfx/about-kusto-data.md) Может использоваться для запроса и изменения данных и метаданных. 
   Она создана на основе REST API Kusto для отправки HTTPS-запросов в целевой кластер Kusto.
* [Kusto.Ingest (клиентская библиотека Kusto).](netfx/about-kusto-ingest.md) Использует библиотеку `Kusto.Data` и расширяет ее возможности для упрощения приема данных.

Описанные выше библиотеки используют интерфейсы API Azure (например, API службы хранилища Azure и API Azure Active Directory).

### <a name="python-libraries"></a>Библиотеки Python

Azure Data Explorer предоставляет клиентскую библиотеку Python, которая позволяет вызывающим объектам отправлять запросы к данным и управляющие команды.
Дополнительные сведения см. в статье [SDK Azure Data Explorer для Python](python/kusto-python-client-library.md).

### <a name="r-library"></a>Библиотека R

Azure Data Explorer предоставляет клиентскую библиотеку R, которая позволяет вызывающим объектам отправлять запросы к данным и управляющие команды.
Дополнительные сведения см. в статье [SDK Azure Data Explorer для R](r/kusto-r-client-library.md).

### <a name="java-sdk"></a>Пакет SDK для Java

Клиентская библиотека Java предоставляет возможности для отправки запросов к кластерам Azure Data Explorer с помощью Java. Дополнительные сведения см. в статье [SDK Azure Data Explorer для Java](java/kusto-java-client-library.md).

### <a name="node-sdk"></a>Пакет SDK для Node

Пакет SDK Azure Data Explorer для Node совместим с выпуском LTS Node (сейчас это 6.14) и создан с помощью ES6.
Дополнительные сведения см. в статье [SDK Azure Data Explorer для Node](node/kusto-node-client-library.md).

### <a name="go-sdk"></a>Пакет SDK для GO

Клиентская библиотека Azure Data Explorer для Go обеспечивает такие возможности, как отправка запросов, контроль и прием данных в кластерах Azure Data Explorer с помощью Go. Дополнительные сведения см. в статье [SDK Azure Data Explorer для Golang](golang/kusto-golang-client-library.md).

### <a name="powershell"></a>PowerShell

Скрипты PowerShell могут использовать библиотеки .NET Framework для Azure Data Explorer. Дополнительные сведения см. в статье [Вызов Azure Data Explorer из PowerShell](powershell/powershell.md).

## <a name="monaco-ide-integration"></a>Интеграция Monaco с IDE

Пакет `monaco-kusto` поддерживает интеграцию с веб-редактором Monaco.
Редактор Monaco, разработанный корпорацией Майкрософт, служит основой для Visual Studio Code.
Дополнительные сведения см. в статье о [пакете monaco-kusto](monaco/monaco-kusto.md).