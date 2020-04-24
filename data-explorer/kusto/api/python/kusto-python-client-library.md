---
title: Azure Data Explorer Python SDK - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается Azure Data Explorer Python SDK в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 5bee1fce1ca76069c34602872b2d4dedeb762ec2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502862"
---
# <a name="azure-data-explorer-python-sdk"></a>Лазурный data Explorer Python SDK

Библиотека клиентов Azure Data Explorer *Kusto Python* предоставляет возможность запроса кластеров Azure Data Explorer с помощью Python. Он совместим с Python 2.x/3.x и поддерживает все типы данных с помощью знакомого интерфейса Python DB API.

Библиотеку можно использовать, например, из [записных книжок Jupyter,](https://jupyter.org/) которые прикрепляются к кластерам Spark, включая, но не исключительно, [экземпляры Azure Databricks.](https://azure.microsoft.com/services/databricks/)

*Kusto Python Ingest Client* — это библиотека python, которая позволяет отправлять данные в службу Azure Data Explorer, т.е. глотать данные. 

* [Установка пакета](https://github.com/Azure/azure-kusto-python#install)

* [Образец запроса Kusto](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py)

* [Выборка, проглядающая данные](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-ingest/tests/sample.py)

* [Репозиторий GitHub](https://github.com/Azure/azure-kusto-python)

    [![альт текст](https://travis-ci.org/Azure/azure-kusto-python.svg?branch=master "azure-kusto-python")](https://travis-ci.org/Azure/azure-kusto-python)

* Пакеты Pypi:

    * [лазурно-кусто-данные](https://pypi.org/project/azure-kusto-data/)
    [![PyPI версия](https://badge.fury.io/py/azure-kusto-data.svg)](https://badge.fury.io/py/azure-kusto-data)
    * [лазурный-кусто-ingest](https://pypi.org/project/azure-kusto-ingest/)
    [![PyPI версия](https://badge.fury.io/py/azure-kusto-ingest.svg)](https://badge.fury.io/py/azure-kusto-ingest)
    * [лазурный-mgmt-kusto](https://pypi.org/project/azure-mgmt-kusto/)
    [![PyPI версия](https://badge.fury.io/py/azure-mgmt-kusto.svg)](https://badge.fury.io/py/azure-mgmt-kusto)