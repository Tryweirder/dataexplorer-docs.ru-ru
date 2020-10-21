---
title: стрреп () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается стрреп () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a5ee36d40035ab2afd19af2da2a0cb174ee365ca
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251432"
---
# <a name="strrep"></a>strrep()

Повторяет заданную [строку](./scalar-data-types/string.md) указанное количество раз.

* Если первый или третий аргумент не относится к строковому типу, он будет принудительно преобразован в строку.

## <a name="syntax"></a>Синтаксис

`strrep(`*значение*,*множитель*, [*Разделитель*]`)`

## <a name="arguments"></a>Аргументы

* *значение*: входное выражение
* *множитель*: положительное целочисленное значение (от 1 до 1024)
* *Разделитель*: необязательное строковое выражение (по умолчанию: пустая строка)

## <a name="returns"></a>Результаты

Значение повторяется в указанное число раз, сцепленное с *разделителем*.

Если *множитель* больше максимального допустимого значения (1024), входная строка будет повторяться 1024 раз.
 
## <a name="example"></a>Пример

```kusto
print from_str = strrep('ABC', 2), from_int = strrep(123,3,'.'), from_time = strrep(3s,2,' ')
```

|from_str|from_int|from_time|
|---|---|---|
|абкабк|123.123.123|00:00:03 00:00:03|