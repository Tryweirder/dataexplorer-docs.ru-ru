---
title: base64_encode_tostring() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны base64_encode_tostring () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: a80b0aa0e3f7e5f330da87f93bbad44e587bcdaf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518060"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

Кодирует строку как строку base64

**Синтаксис**

`base64_encode_tostring(`*Строка*`)`

**Аргументы**

* *Строка:* Строка ввода, которая будет кодирована как строка base64.

**Возвращает**

Возвращает строку, закодированную как строку base64.

* Для расшифровки базовых строк на строку UTF-8 см. [base64_decode_tostring()](base64_decode_tostringfunction.md)
* Для расшифровки базовых строк на массив длинных значений см. [base64_decode_toarray()](base64_decode_toarrayfunction.md)


**Пример**

```kusto
print Quine=base64_encode_tostring("Kusto")
```

|Квин   |
|--------|
|S3VzdG8|
