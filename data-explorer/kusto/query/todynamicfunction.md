---
title: todynamic(), toobject() - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны тодинамические (), тообъект() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 138b0f978df699817c5dc5c14bafc4c06a95afc7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506160"
---
# <a name="todynamic-toobject"></a>тодинамический(), объект()

Интерпретирует `string` значение [JSON](https://json.org/) и возвращает [`dynamic`](./scalar-data-types/dynamic.md)значение как . 

Он превосходит [функцию extractjson()](./extractjsonfunction.md) при необходимости извлечения более одного элемента объекта соединения JSON.

Прозвища [parse_json ()](./parsejsonfunction.md) функции.

**Синтаксис**

`todynamic(`*Json*`)`
`toobject(`*Json*`)`

**Аргументы**

* *json*: Документ JSON.

**Возвращает**

Объект типа `dynamic` , определенный в *json*.

*Примечание*: Предпочитаюиспользовать [динамические ()](./scalar-data-types/dynamic.md) по возможности.