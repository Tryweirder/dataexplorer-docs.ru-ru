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
ms.openlocfilehash: 7d6db9ba7cbb7b1fbaaee7325043fed287673178
ms.sourcegitcommit: acd8aba5ed957bee4a7361057387145f9d3ef4e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91734025"
---
# <a name="query-throttling-policy"></a>Политика регулирования запросов

Определите политику регулирования запросов, чтобы ограничить количество параллельных запросов, выполняемых кластером в одно и то же время. Политику можно изменить во время выполнения и сразу после завершения команды ALTER Policy.

* Используется [`.show cluster policy querythrottling`](query-throttling-policy-commands.md#show-cluster-policy-querythrottling) для отображения текущей политики регулирования запросов кластера.
* Используется [`.alter cluster policy querythrottling`](query-throttling-policy-commands.md#alter-cluster-policy-querythrottling) для задания текущей политики регулирования запросов кластера.
* Используйте [`.delete cluster policy querythrottling`](query-throttling-policy-commands.md#delete-cluster-policy-querythrottling) для удаления текущей политики регулирования запросов кластера.

> [!NOTE]
> Если не определить политику регулирования запросов, большое количество одновременных запросов может привести к недоступности кластера или ухудшению производительности.
