---
title: Сведения об API Azure Data Explorer (Azure Data Explorer) | Документация Майкрософт
description: В этой статье представлены сведения об API Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: c791ae0be0c895a4744e761c58e7d455aa0a22b9
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610275"
---
# <a name="azure-data-explorer-api-overview"></a>Описание API Azure Data Explorer

Служба Azure Data Explorer поддерживает следующие конечные точки для взаимодействия:

1. Конечная точка [REST API](#rest-api), с помощью которой можно запрашивать данные и управлять ими в Azure Data Explorer.
   Эта конечная точка поддерживает [язык запросов Kusto](../query/index.md) для запросов и [команд управления](../management/index.md).
2. Конечная точка [MS-TDS](#ms-tds), которая реализует подмножество возможностей протокола Microsoft TDS (поток табличных данных), используемых в продуктах Microsoft SQL Server.
   Эта конечная точка в основном применяется для существующих средств, которые уже умеют взаимодействовать с конечной точкой SQL Server для отправки запросов.
3. Конечная точка [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftkusto), которая является стандартным решением для управления такими ресурсами в службах Azure, как кластеры Azure Data Explorer.

Azure Data Explorer предоставляет несколько клиентских библиотек, которые используют перечисленные выше конечные точки для упрощения программного доступа:

1. Пакет SDK для .NET
2. Пакет SDK для Python
3. Пакет SDK для Java
4. Пакет SDK для Node
5. Пакет SDK для GO
6. PowerShell
7. R

## <a name="rest-api"></a>REST API

Основным методом взаимодействия со службой Azure Data Explorer считается интерфейс REST API этой службы. Через эту полностью документированную конечную точку вызывающие объекты могут выполнять следующее:

1. Данные запросов
2. запросы к метаданным и их изменение;
3. Прием данных
4. запрос состояния работоспособности службы;
5. Управление ресурсами

Разные службы Azure Data Explorer взаимодействуют между собой через один и тот же общедоступный интерфейс REST API.

Помимо поддержки запросов к Azure Data Explorer с помощью REST API, доступны несколько клиентских библиотек, которые позволяют работать со службой, не используя протокол REST API.

## <a name="ms-tds"></a>MS-TDS

В качестве альтернативы подключению к Azure Data Explorer для запроса данных Azure Data Explorer поддерживает протокол связи Microsoft SQL Server (MS-TDS) и ограниченно поддерживает выполнение запросов T-SQL. Это позволяет пользователям выполнять запросы к Azure Data Explorer с помощью знакомого синтаксиса запросов (T-SQL) и привычных клиентских средств для работы с базами данных (например, LINQPad, sqlcmd, Tableau, Excel и Power BI).

Дополнительные сведения об MS-TDS см. на [этой странице](tds/index.md).

## <a name="net-framework-libraries"></a>Библиотеки платформы .NET

Библиотеки .NET Framework — это рекомендуемый способ вызова функций Azure Data Explorer программными средствами.
Здесь предоставляется ряд библиотек:

- [**Kusto.Data (клиентская библиотека Kusto)** ](./netfx/about-kusto-data.md), которая поддерживает запрос данных, запрос и изменение метаданных.
- [**Kusto.Ingest (библиотека приема Kusto)** ](netfx/about-kusto-ingest.md), которая использует библиотеку Kusto.Data и расширяет ее возможности для упрощения приема данных.


**Клиентская библиотека Kusto** (Kusto.Data) создана на основе REST API Kusto для отправки HTTPS-запросов в целевой кластер Kusto. 

**Библиотека приема Kusto** (Kusto.Ingest) использует Kusto.Data.



Все описанные выше библиотеки используют интерфейсы API Azure (например, API службы хранилища Azure и API Azure Active Directory).

## <a name="python-libraries"></a>Библиотеки Python

Azure Data Explorer предоставляет клиентскую библиотеку Python, которая позволяет вызывающим объектам отсылать запросы к данным и управляющие команды.

## <a name="r-library"></a>Библиотека R

Azure Data Explorer предоставляет клиентскую библиотеку R, которая позволяет вызывающим объектам отсылать запросы к данным и управляющие команды.



## <a name="using-azure-data-explorer-from-powershell"></a>Использование Azure Data Explorer из PowerShell

Скрипты PowerShell могут использовать библиотеки .NET Framework для Azure Data Explorer.
Пример такого использования см. в статье [о вызове Azure Data Explorer из PowerShell](powershell/powershell.md).

## <a name="ide-integration"></a>Интеграция с IDE

Пакет `monaco-kusto` поддерживает интеграцию с веб-редактором Monaco Editor, который разработан корпорацией Майкрософт и лежит в основе Visual Studio Code.
Подробнее о пакете [monaco-kusto](monaco/monaco-kusto.md).