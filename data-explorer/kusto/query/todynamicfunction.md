---
title: todynamic (), тубжект () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается todynamic (), тубжект () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e11a4d450275fb4d596bd9618c20ef6cefcb0531
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350743"
---
# <a name="todynamic-toobject"></a>todynamic(), toobject()

Интерпретирует `string` как [значение JSON](https://json.org/) и возвращает значение в виде [`dynamic`](./scalar-data-types/dynamic.md) . 

Если необходимо извлечь более одного элемента составного объекта JSON, лучше использовать [функцию екстрактжсон ()](./extractjsonfunction.md) .

Псевдонимы для функции [parse_json ()](./parsejsonfunction.md) .

## <a name="syntax"></a>Синтаксис

`todynamic(`*Формат* `)` 
 JSON `toobject(` *Формат JSON*`)`

## <a name="arguments"></a>Аргументы

* *JSON*: документ JSON.

## <a name="returns"></a>Результаты

Объект типа `dynamic` , определенный в *json*.

*Примечание*. предпочтительнее использовать [dynamic ()](./scalar-data-types/dynamic.md) , если это возможно.