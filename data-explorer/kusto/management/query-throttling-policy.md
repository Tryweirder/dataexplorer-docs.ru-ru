---
title: Политика регулирования запросов в Azure обозреватель данных
description: В этой статье описывается политика регулирования запросов в Azure обозреватель данных
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 0f50170e3ccfab20d434a90188baf511de056cef
ms.sourcegitcommit: 541164d8745022906b1da4aaca41ec15393f85d5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570571"
---
# <a name="query-throttling-policy"></a>Политика регулирования запросов

Определите политику регулирования запросов, чтобы ограничить количество параллельных запросов, которые кластер может выполнять одновременно. Эта политика защищает кластер от перегрузки с более параллельными запросами, чем он может поддерживаться. Политику можно изменить во время выполнения и сразу после завершения команды ALTER Policy.

* Используется [`.show cluster policy querythrottling`](query-throttling-policy-commands.md#show-cluster-policy-querythrottling) для отображения текущей политики регулирования запросов кластера.
* Используется [`.alter cluster policy querythrottling`](query-throttling-policy-commands.md#alter-cluster-policy-querythrottling) для задания текущей политики регулирования запросов кластера.
* Используйте [`.delete cluster policy querythrottling`](query-throttling-policy-commands.md#delete-cluster-policy-querythrottling) для удаления текущей политики регулирования запросов кластера.

> [!NOTE]
> Не изменяйте политику регулирования запросов без тщательного тестирования. Изменения могут привести к снижению производительности, если ресурсы кластера не масштабируются в соответствии с загрузкой, и в результате запросы могут начаться с ошибкой. 
