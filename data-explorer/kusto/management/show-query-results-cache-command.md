---
title: Отображение кэша результатов запроса в Azure обозреватель данных
description: В этой статье описывается, как отобразить кэш результатов запроса в обозреватель данных Azure.
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 84805cae07049af4ffa8a2fdb82e637261140f8f
ms.sourcegitcommit: cf1da667be12656a8c4727c23144421b5a4b1099
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/21/2020
ms.locfileid: "86565428"
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
|Количество  |`long`| Число уникальных результатов запроса, хранящихся в кэше.
