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
ms.openlocfilehash: 6c6d9aedee17592ac1eb1b43e93dead80eb9fc61
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128858"
---
# <a name="function-types"></a>Типы функций

**Функции** — это многократно используемые запросы или части запросов. Kusto поддерживает несколько типов функций:

* **Хранимые функции**, то есть определяемые пользователем функции, которые хранятся и управляются в базе данных в виде сущностей схемы.
  Подробные сведения см. [здесь](../schema-entities/stored-functions.md).
* **Определяемые в запросе функции**, то есть определяемые пользователем функции, которые определяются и используются в пределах одного конкретного запроса. Определение таких функций выполняется [инструкцией let](../letstatement.md).
  Подробные сведения см. [здесь](./user-defined-functions.md).
* **Встроенные функции**, то есть жестко запрограммированные запросы (которые определены в Kusto и не могут быть изменены пользователями).