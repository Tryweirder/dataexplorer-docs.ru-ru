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
adobe-target: true
ms.openlocfilehash: 234ae530fcd664d8d12418ed85d8394385bebf75
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359946"
---
# <a name="function-types"></a>Типы функций

**Функции** — это многократно используемые запросы или части запросов. Kusto поддерживает несколько типов функций:

* **Хранимые функции**, то есть определяемые пользователем функции, которые хранятся и управляются в базе данных в виде сущностей схемы.
  Подробные сведения см. [здесь](../schema-entities/stored-functions.md).
* **Определяемые в запросе функции**, то есть определяемые пользователем функции, которые определяются и используются в пределах одного конкретного запроса. Определение таких функций выполняется [инструкцией let](../letstatement.md).
  Подробные сведения см. [здесь](./user-defined-functions.md).
* **Встроенные функции**, то есть жестко запрограммированные запросы (которые определены в Kusto и не могут быть изменены пользователями).