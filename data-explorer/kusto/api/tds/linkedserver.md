---
title: Кусто как связанный сервер с сервера S'L - Azure Data Explorer Документы Майкрософт
description: В этой статье Кусто описывается как связанный сервер с сервера S'L в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 77db975f2bd7c5c1d747902248070664cd020e39
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523466"
---
# <a name="kusto-as-linked-server-from-sql-server"></a>Kusto как связанный сервер с сервера S'L

Сервер S'L на месте позволяет прикрепить связанный сервер. Эта функция позволяет создавать запросы, объединяющие данные с сервера S'L и с связанных серверов.

Можно использовать Kusto в качестве связанного сервера через подключение ODBC.

1. Служба сервера S'L (на территории) должна использовать активную учетную запись каталога (не учетную запись службы по умолчанию), которая позволяет подключиться к Kusto с помощью AAD.
2. Установите новейший драйвер ODBC для S'L Server 2017 (он также поставляется с SSMS 18):https://www.microsoft.com/download/details.aspx?id=56567
3. Подготовьте строку dSN с меньшим подключением для драйвера `Driver={ODBC Driver 17 for SQL Server};Server=<cluster>.kusto.windows.net;Database=<database>;Authentication=ActiveDirectoryIntegrated;Language=any@MaxStringSize:4000`ODBC для конкретного кластера и базы данных Kusto: . Языковой параметр добавляется для настройки Kusto для кодирования строк как NVARCHAR (4000). Подробнее об этом обходе можно на [ODBC.](./clients.md#odbc)
4. Создайте связанный сервер со следующими 3 параметрами, определенными: ![alt text](../images/linkedserverconnection.png "связанное подключение сервера")
5. Вкладка безопасности должна быть определена с помощью этого параметра: ![alt text](../images/linkedserverlogin.png "связанный вход сервера")

Теперь можно заказать данные из Кусто, как это:
```sql
SELECT * FROM OpenQuery(LINKEDSERVER, 'SELECT * FROM <KustoStoredFunction>[(<Parameters>)]')
```

Примечания.
1. Рекомендуется использовать [сохраненные функции](../../query/schema-entities/stored-functions.md) Kusto для извлечения данных из Кусто. Сохраненная функция может включать в себя всю логику, необходимую для эффективного запроса от Kusto, авторего на родном языке [КЗЛ](../../query/index.md) и управляемого определенными значениями параметра. Синтаксис Т-СЗЛ для вызова функции хранения Kusto идентичен вызову табулярной функции S'L.
2. Сервер S'L не поддерживает вызов удаленных табличной функции с связанных серверов внутри своих собственных запросов. Обход для этого ограничения заключается в использовании `OpenQuery` для выполнения удаленного запроса на связанном сервере. Таким образом, табулярная функция вызывается не на сервере S'L directy, а в запросе, который выполняется на связанном сервере. Внешний запрос T-S'L может быть использован для объединения между данными на сервере `OpenQuery`S'L и данными, возвращенными из сохраненной функции Kusto через.