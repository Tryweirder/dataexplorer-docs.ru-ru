---
title: Запрос данных с помощью библиотеки Python в Azure Data Explorer
description: В этой статье вы узнаете, как заставить данные из Azure Data Explorer с помощью Python.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: ebd65f2dcbb0040b764290627bbfd2901aa9a7d3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501442"
---
# <a name="query-data-using-the-azure-data-explorer-python-library"></a>Запрос данных с помощью библиотеки Python в Azure Data Explorer

В этой статье вы задавили запрос данных с помощью Azure Data Explorer. Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии.

Обозреватель данных Azure предоставляет [клиентскую библиотеку данных для Python](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Эта библиотека позволяет выполнять запросы данных из кода. Подключитесь к таблице *в кластере справки,* который мы создали для обучения. Вы можете заразить таблицу в этом кластере и вернуть результаты.

Эта статья также доступна в виде [ноутбука Azure](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb).

## <a name="prerequisites"></a>Предварительные требования

* [Питон 3,4 "](https://www.python.org/downloads/)

* Учетная запись электронной почты организации, которая входит в Azure Active Directory (AAD)

## <a name="install-the-data-library"></a>Установка библиотеки данных

Установите библиотеку *azure-kusto-data*.

```
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Добавление операторов и констант импорта

Импортируйте классы из библиотеки, а также *pandas*, библиотеку анализа данных.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

Чтобы проверить подлинность приложения, обозреватель данных Azure использует идентификатор клиента AAD. Чтобы найти идентификатор клиента, используйте следующий URL-адрес, заменив домен на *имя_вашего_домена*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Например, если ваш домен называется *contoso.com*, URL-адрес будет следующим: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Щелкните этот URL-адрес, чтобы просмотреть результаты. Первая строка выглядит следующим образом:

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

В данном случае идентификатор клиента — `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Перед выполнением этого кода задайте значение для AAD_TENANT_ID.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE = "Samples"
```

Создайте строку подключения. В этом примере используется проверка подлинности устройства для доступа к кластеру. Вы также можете использовать [сертификат приложения AAD,](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24) [ключ приложения AAD,](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20) [а также пользователя и пароль AAD.](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34)

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Подключение к обозревателю данных Azure и выполнение запроса

Выполните запрос к кластеру и сохраните выходные данных в кадр данных. При выполнении код возвращает примерно следующее сообщение: *Чтобы войти, откройте страницу https://microsoft.com/devicelogin с помощью веб-браузера и введите код F3W4VWZDM для проверки подлинности*. Следуйте инструкциям по входу, а затем выполните следующий блок кода.

```python
KUSTO_CLIENT = KustoClient(KCSB)
KUSTO_QUERY = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>Изучение данных в кадре данных

После входа запрос возвращает результаты, которые сохраняются в кадре данных. С результатами можно работать так же, как с любыми кадрами данных.

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

Вы увидите десять самых первых результатов из таблицы StormEvents.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Проем данных с помощью библиотеки Azure Data Explorer Python](python-ingest-data.md)
