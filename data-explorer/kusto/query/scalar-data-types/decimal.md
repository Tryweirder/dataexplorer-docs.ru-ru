---
title: Тип десятичных данных - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается тип десятичных данных в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 62fd745c804ad4a50652e09cd722c17a4a61daac
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509900"
---
# <a name="the-decimal-data-type"></a>Тип десятичных данных

Тип `decimal` данных представляет собой 128-битное десятичное число.

Буквы типа `decimal` данных имеют такое же представление, что и . NET's `System.Data.SqlTypes.SqlDecimal`.

`decimal(1.0)`, `decimal(0.1)`, `decimal(1e5)` и все буквальные `decimal`типа .

Существует несколько специальных буквальных форм:
* `decimal(null)`: Является [нулевая стоимость](null-values.md).