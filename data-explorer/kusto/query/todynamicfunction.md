---
title: todynamic (), тубжект () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается todynamic (), тубжект () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: c92ff96b3ae6a0369de1c8fa715e64499fb051b7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250483"
---
# <a name="todynamic-toobject"></a>todynamic(), toobject()

Интерпретирует `string` как [значение JSON](https://json.org/) и возвращает значение в виде [`dynamic`](./scalar-data-types/dynamic.md) . 

Если необходимо извлечь более одного элемента составного объекта JSON, лучше использовать [функцию екстрактжсон ()](./extractjsonfunction.md) .

Псевдонимы для функции [parse_json ()](./parsejsonfunction.md) .

> [!NOTE]
> Предпочитать использовать [dynamic ()](./scalar-data-types/dynamic.md) , если это возможно.

## <a name="syntax"></a>Синтаксис

`todynamic(`*Формат* `)` 
 JSON `toobject(` *Формат JSON*`)`

## <a name="arguments"></a>Аргументы

* *JSON*: документ JSON.

## <a name="returns"></a>Результаты

Объект типа `dynamic` , определенный в *json*.
