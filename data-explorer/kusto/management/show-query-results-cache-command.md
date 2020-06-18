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
ms.openlocfilehash: bcbdb59355ce0461d735cbea902551c219479fd2
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943109"
---
# <a name="show-query-results-cache"></a>. Отображение кэша результатов запроса

Возвращает таблицу, показывающую статистические данные, связанные с [кэшем результатов запросов](../query/query-results-cache.md).

**Синтаксис**

`.show` `query` `results` `cache`

**Выходные данные**
 
|Выходной параметр |Тип |Описание 
|---|---|---
|Попадания  |long |Число попаданий в кэш.
|"Промахи"  |long |Число промахов кэша.
|качекапаЦитинбитес |long |Емкость кэша в байтах.
|уседбитес  |long |Используемое пространство кэша.
|Count  |long | Число уникальных результатов запроса, хранящихся в кэше.

**Ограничения**

* Выходные данные команды в настоящее время отражают только статистику кэша, собранную узлом, на который был получен запрос.
* Команда отображает только журнал "Недавние".
