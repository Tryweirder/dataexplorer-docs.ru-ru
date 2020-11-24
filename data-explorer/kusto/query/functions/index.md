---
title: Функции в Azure Data Explorer | Документация Майкрософт
description: В этой статье описываются функции в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.localizationpriority: high
ms.openlocfilehash: 1e0b3f339a755531d8db146ed2dc478ebb5a888d
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513035"
---
# <a name="function-types"></a>Типы функций

**Функции** — это многократно используемые запросы или части запросов. Kusto поддерживает несколько типов функций:

* **Хранимые функции**, то есть определяемые пользователем функции, которые хранятся и управляются в базе данных в виде сущностей схемы.
  Подробные сведения см. [здесь](../schema-entities/stored-functions.md).
* **Определяемые в запросе функции**, то есть определяемые пользователем функции, которые определяются и используются в пределах одного конкретного запроса. Определение таких функций выполняется [инструкцией let](../letstatement.md).
  Подробные сведения см. [здесь](./user-defined-functions.md).
* **Встроенные функции**, то есть жестко запрограммированные запросы (которые определены в Kusto и не могут быть изменены пользователями).