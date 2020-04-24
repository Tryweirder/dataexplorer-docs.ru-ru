---
title: string_size () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны string_size() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7886e7b8fd5039c9b197ae435bce4f40b93e5038
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506891"
---
# <a name="string_size"></a>string_size()

Возвращает размер, в байтах, входиной строки.

**Синтаксис**

`string_size(`*Источник*`)`

**Аргументы**

* *источник*: Строка источника, которая будет измерена для размера строки.

**Возвращает**

Возвращает длину, в байтах, входиной строки.

**Примеры**

```kusto
print size = string_size("hello")
```

|size|
|---|
|5|

```kusto
print size = string_size("⒦⒰⒮⒯⒪")
```

|size|
|---|
|15|