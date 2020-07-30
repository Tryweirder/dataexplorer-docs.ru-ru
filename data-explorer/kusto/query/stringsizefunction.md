---
title: string_size () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается string_size () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4832d00703ab9e6d1478af5b3f45ec294dfb79ce
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350916"
---
# <a name="string_size"></a>string_size()

Возвращает размер входной строки в байтах.

## <a name="syntax"></a>Синтаксис

`string_size(`*источника*`)`

## <a name="arguments"></a>Аргументы

* *Source*: исходная строка, которая будет измеряться для размера строки.

## <a name="returns"></a>Результаты

Возвращает длину входной строки в байтах.

## <a name="examples"></a>Примеры

```kusto
print size = string_size("hello")
```

|размер;|
|---|
|5|

```kusto
print size = string_size("⒦⒰⒮⒯⒪")
```

|размер;|
|---|
|15|