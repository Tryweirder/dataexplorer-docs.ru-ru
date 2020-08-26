---
title: Синхронизация Kusto в Azure обозреватель данных
description: В этой статье описывается синхронизация Kusto в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: zivc
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/12/2019
ms.openlocfilehash: 6e1cd00cb84ad7b4d62429a932d3ffe298a83766
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875197"
---
# <a name="sync-kusto"></a>Синхронизация Kusto

Sync Kusto — это средство, которое позволяет пользователям синхронизировать различные сущности схемы Kusto, такие как схемы таблиц и хранимые функции. Эта синхронизация выполняется между локальной файловой системой, базой данных Azure обозреватель данных и Azure dev Ops репозиториев.

Kusto синхронизации доступен [на сайте GitHub](https://github.com/microsoft/synckusto).