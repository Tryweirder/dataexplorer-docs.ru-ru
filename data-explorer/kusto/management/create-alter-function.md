---
title: функция .create-or-alter - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается функция .create-or-alter в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: 9e9c24f7fda44d6c44b8f78d8622b525268a341a
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744290"
---
# <a name="create-or-alter-function"></a>.create-or-alter function

Создает функцию хранения или изменяет существующую функцию и хранит ее в метаданных базы данных.

```kusto
.create-or-alter function [with (docstring = '<description>', folder='<name>')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```

Если функция с предоставленной *Функцией* Не существует в метаданных базы данных, команда создает новую функцию. Если функция уже существует, эта функция будет изменена.

**Пример**

```kusto
.create-or-alter function  with (docstring = 'Demo function with parameter', folder='MyFolder') TestFunction(myLimit:int)
{
    StormEvents | take myLimit 
} 
```

|Имя|Параметры|Текст|Папка|DocString|
|---|---|---|---|---|
|TestFunction|(myLimit:int)|- StormEvents &#124; принять myLimit|MyFolder|Функция демо с параметром|
