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
ms.openlocfilehash: acd1b5328150e61bc81930f94b8ea9e8025e1ebb
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804089"
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

## <a name="returns"></a>Возвращаемое значение

Объект типа `dynamic` , определенный в *json*.
