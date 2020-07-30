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
ms.openlocfilehash: 7b7a96a01a4ec2b6c84609b2f9c518637d174390
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349896"
---
# <a name="show-database-cache-query_results"></a>. показывать query_results кэша базы данных

Возвращает таблицу, показывающую статистику, связанную с [кэшем результатов запросов](../query/query-results-cache.md) , сделанным в базе данных контекста.

**Синтаксис**

`.show database query results cache`

**Выходные данные**
 
|Выходной параметр |Тип |Описание 
|---|---|---
|NodeId|`string`|Идентификатор узла кластера.
|Попадания  |`long`|Число попаданий в кэш.
|"Промахи"  |`long`|Число промахов кэша.
|качекапаЦитинбитес |`long` |Емкость кэша в байтах.
|уседбитес  |`long` |Используемое пространство кэша.
|Счетчик  |`long`| Число уникальных результатов запроса, хранящихся в кэше.
