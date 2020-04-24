---
title: Обзор управления сохраненными функциями - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается обзор управления сохраненными функциями в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: f3768c6252a96215d37bd9f19a44cbf4d3afc731
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520967"
---
# <a name="stored-functions-management-overview"></a>Обзор управления сохраненными функциями
В этом разделе описаны команды управления, используемые для создания и изменения следующих [функций, определенных пользователем:](../query/functions/user-defined-functions.md)

|Компонент |Описание|
|---------|-----------|
|[функция .alter](alter-function.md) |Изменяет существующую функцию и хранит ее в метаданных базы данных |
|[docstring функции .alter](alter-docstring-function.md) |Изменяет значение DocString существующей функции |
|[папка функции .alter](alter-folder-function.md) |Изменяет значение Folder существующей функции |
|[функция .create](create-function.md) |Создает сохраненную функцию |
|[функция .create-or-alter](create-alter-function.md) |Создает сохраненную функцию или изменяет существующую функцию и хранит ее внутри метаданных базы данных |
|[функция .drop и функции .drop](drop-function.md) |Выпадает функция (или функции) из базы данных |
|[функции .show и функции .show](show-function.md) |Списки всех сохраненных функций или определенной функции в выбранной в настоящее время базе данных |