---
title: Согласованность запросов в Azure обозреватель данных
description: В этой статье описывается согласованность запросов в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: 8b4d1df4dc9a035764f9d50a4f9c4dcf452da67e
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512271"
---
# <a name="query-consistency"></a>Согласованность запросов

Kusto поддерживает две модели согласованности запросов: **strong** и **слабый**.

Строго согласованные запросы (по умолчанию) имеют гарантию «Read-My-Changes». Если вы отправите команду управления и получаете подтверждение о том, что команда выполнена успешно, будет гарантировано, что все запросы, приведенные ниже, будут наблюдать за результатами выполнения команды.

Ненадежные согласованные запросы, которые должны быть явно включены клиентом, не имеют такой гарантии. Клиенты, выполняющие запросы, могут наблюдать за задержкой (обычно 1-2 минут) между изменениями и запросами, отражающими эти изменения.

Преимущество слабо противоречивых запросов заключается в том, что уменьшается нагрузка на узел кластера, обрабатывающая изменения базы данных. Как правило, рекомендуется сначала испытать строго целостную модель. При необходимости переключитесь на использование ненадежных запросов.

Чтобы переключиться на неустойчивые запросы, задайте `queryconsistency` свойство при [вызове REST API](../api/rest/request.md). Пользователи клиента .NET также могут задать его в [строке подключения Kusto](../api/connection-strings/kusto.md) или в качестве флага в [свойствах запроса клиента](../api/netfx/request-properties.md).
