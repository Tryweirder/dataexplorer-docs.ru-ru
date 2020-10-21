---
title: string_size () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается string_size () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea06e7e41ebe48e09839109745f3fe7ba5a96e7a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251472"
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