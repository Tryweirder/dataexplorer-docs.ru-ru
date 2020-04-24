---
title: Kusto Ping REST API - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается Kusto Ping REST API в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 14a3d3dd641ff435784eac4e813d98bac8c4a552
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502590"
---
# <a name="kusto-ping-rest-api"></a>Кусто Пинг REST API

Ping REST API предоставляется для того, чтобы сетевые устройства, такие как балансеры нагрузки, проверяли простую реакцию сети на передние компоненты кластера без состояния. При применении глагола GET к этой конечной точке кластер отвечает, возвращая 200 OK HTTP ответ. REST API не является аутентификацией (вызывающему не нужно отправлять заголовок HTTP). `Authorization`

- Путь: `/v1/rest/ping`
- Глагол:`GET`
- Действия: Ответ `200 OK` с сообщением
- Аргументы: Нет