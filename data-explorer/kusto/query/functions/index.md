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
ms.openlocfilehash: f1307b54c9f0b7a948925dd4eaa4d1f2e89d8070
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490316"
---
# <a name="functions"></a>Функции

**Функции** — это многократно используемые запросы или части запросов. Kusto поддерживает несколько типов функций:

* **Хранимые функции**, то есть определяемые пользователем функции, которые хранятся и управляются в базе данных в виде сущностей схемы.
  Подробные сведения см. [здесь](../schema-entities/stored-functions.md).
* **Определяемые в запросе функции**, то есть определяемые пользователем функции, которые определяются и используются в пределах одного конкретного запроса. Определение таких функций выполняется [инструкцией let](../letstatement.md).
  Подробные сведения см. [здесь](./user-defined-functions.md).
* **Встроенные функции**, то есть жестко запрограммированные запросы (которые определены в Kusto и не могут быть изменены пользователями).