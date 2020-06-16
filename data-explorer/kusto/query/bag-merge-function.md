---
title: bag_merge () — обозреватель данных Azure
description: В этой статье описывается bag_merge () в Azure обозреватель данных.
services: data-explorer
author: elgevork
ms.author: elgevork
ms.reviewer: ''
ms.service: data-explorer
ms.topic: reference
ms.date: 06/14/2020
ms.openlocfilehash: f5ca4888b0c3aad976d78c10bbbfa0810483c75b
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84784605"
---
# <a name="bag_merge"></a>bag_merge ()

Объединяет входные динамические объекты контейнера свойств в динамический объект контейнера свойств.

**Синтаксис**

`bag_merge(`*динамический объект* `,` *динамический объект*`)`

**Аргументы**

* Введите динамические объекты, разделенные запятыми. Функция поддерживает от 2 до 64 входных динамических объектов.

**Возвращает**

Возвращает `dynamic` контейнер свойств. Результаты слияния всех входных объектов контейнера свойств.
Если ключ встречается в нескольких входных объектах, будет выбрано произвольное значение (из возможных значений для этого ключа).

**Пример**

Выражение:

`print result = bag_merge(dynamic({ 'A1':12, 'B1':2, 'C1':3}), dynamic({ 'A2':81, 'B2':82, 'A1':1}))`

Принимает значение:

`{
  "A1": 12,
  "B1": 2,
  "C1": 3,
  "A2": 81,
  "B2": 82
}`
