---
title: Kusto в качестве связанного сервера из SQL Server — Azure обозреватель данных
description: В этой статье описывается Kusto в качестве связанного сервера из SQL Server в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 103d39f7fdd10f0375e4a530d51cd17f7cdcec51
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799600"
---
# <a name="kusto-as-a-linked-server-from-the-sql-server"></a>Kusto в качестве связанного сервера из SQL Server

Локальная среда SQL Server позволяет подключить связанный сервер и позволяет создавать запросы, объединяющие данные из SQL Server и связанных серверов.

Kusto можно использовать как связанный сервер через подключение ODBC.
SQL Server локальной службе необходимо использовать учетную запись Active Directory (не учетную запись службы по умолчанию), которая позволяет подключаться к Azure обозреватель данных с помощью Azure Active Directory (Azure AD).

1. Установите последнюю версию драйвера ODBC для SQL Server 2017 (он также поставляется с SSMS 18):https://www.microsoft.com/download/details.aspx?id=56567
1. Подготовьте строку подключения без имени DSN для драйвера ODBC для конкретного кластера Azure обозреватель данных и базы данных: `Driver={ODBC Driver 17 for SQL Server};Server=<cluster>.kusto.windows.net;Database=<database>;Authentication=ActiveDirectoryIntegrated;Language=any@MaxStringSize:4000`. Параметр Language добавляется для настройки обозреватель данных Azure для кодирования строк как NVARCHAR (4000). Дополнительные сведения об этом обходном пути см. в разделе [ODBC](./clients.md#odbc).
1. Создайте связанный сервер с параметрами, на которые указывает красная стрелка.

:::image type="content" source="../images/linkedserverconnection.png" alt-text="соединение с связанным сервером":::

1. Определите безопасность с параметром, на который указывает красная стрелка. 

:::image type="content" source="../images/linkedserverlogin.png" alt-text="имя входа связанного сервера":::

Запрос данных из Kusto:

```sql
SELECT * FROM OpenQuery(LINKEDSERVER, 'SELECT * FROM <KustoStoredFunction>[(<Parameters>)]')
```

> [!NOTE]
> 1. Используйте [хранимые функции](../../query/schema-entities/stored-functions.md) Kusto для извлечения данных из Azure обозреватель данных. Хранимая функция может включать всю логику, необходимую для эффективных запросов от Kusto, созданных на собственном языке [ККЛ](../../query/index.md) , и управления ими с помощью указанных значений параметров. Синтаксис T-SQL для вызова хранимой функции Kusto идентичен вызову табличной функции SQL.
> 1. SQL Server не поддерживает вызов удаленных табличных функций из связанных серверов внутри собственных запросов. Обходной путь для этого ограничения заключается в `OpenQuery` использовании для исполнения удаленных запросов на связанном сервере. Таким образом, табличная функция вызывается не в каталоге SQL Server, а в запросе, который выполняется на связанном сервере. Внешний запрос T-SQL можно использовать для объединения данных на сервере SQL Server и данных, возвращаемых хранимой функцией Kusto с помощью `OpenQuery`.
