---
title: Базы данных в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются базы данных в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 3d981a4b56eef689081a4bc6a622221c126efa2e
ms.sourcegitcommit: 8b8228fe18e6408673891374a8048a7a3723921c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96516070"
---
# <a name="databases"></a>Базы данных

Kusto соответствует модели отношения хранения данных, где сущность верхнего уровня — `database` . 

Кластер Kusto может содержать несколько баз данных, где каждая база данных будет содержать собственную коллекцию [таблиц](tables.md), [хранимых функций](stored-functions.md)и [внешних таблиц](externaltables.md).
Каждая база данных имеет собственный набор разрешений на основе [модели управления доступом на основе ролей (RBAC)](../../management/access-control/index.md) .

**Примечания**  

* Имена баз данных должны соответствовать правилам для [имен сущностей](./entity-names.md).
* Максимальное число баз данных на кластер — 10 000.
