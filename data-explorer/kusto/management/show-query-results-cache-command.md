---
title: Отображение кэша результатов запроса в Azure обозреватель данных
description: В этой статье описывается, как отобразить кэш результатов запроса в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: e037b6ed01a2eba7c7370d75a4efea0f7cbc1756
ms.sourcegitcommit: 92b8057a36bd7daa16226f1526b29253bceb3602
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91402724"
---
# <a name="show-database-cache-query_results"></a>. показывать query_results кэша базы данных

Возвращает таблицу, показывающую статистику, связанную с [кэшем результатов запросов](../query/query-results-cache.md) , сделанным в базе данных контекста.

**Синтаксис**

`.show database cache query_results`

**Выходные данные**
 
|Выходной параметр |Тип |Описание 
|---|---|---
|NodeId|`string`|Идентификатор узла кластера.
|Попадания  |`long`|Число попаданий в кэш.
|"Промахи"  |`long`|Число промахов кэша.
|качекапаЦитинбитес |`long` |Емкость кэша в байтах.
|уседбитес  |`long` |Используемое пространство кэша.
|Счетчик  |`long`| Число уникальных результатов запроса, хранящихся в кэше.
