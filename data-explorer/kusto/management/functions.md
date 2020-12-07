---
title: Общие сведения об управлении хранимыми функциями — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются общие сведения об управлении хранимыми функциями в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: 4354538b206ee86e34941718bcf6d74130647fb6
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321358"
---
# <a name="stored-functions-management-overview"></a>Общие сведения об управлении хранимыми функциями
В этом разделе описываются команды управления, используемые для создания и изменения следующих [определяемых пользователем функций](../query/functions/user-defined-functions.md).

|Функция |Описание|
|---------|-----------|
|[`.alter function`](alter-function.md) |Изменяет существующую функцию и сохраняет ее в метаданных базы данных. |
|[`.alter function docstring`](alter-docstring-function.md) |Изменяет значение DocString существующей функции. |
|[`.alter function folder`](alter-folder-function.md) |Изменяет значение папки существующей функции |
|[`.create function`](create-function.md) |Создает хранимую функцию |
|[`.create-or-alter function`](create-alter-function.md) |Создает хранимую функцию или изменяет существующую функцию и сохраняет ее в метаданных базы данных. |
|[`.drop function` перетаскивани `.drop functions`](drop-function.md) |Удаляет функцию (или функции) из базы данных |
|[`.show functions` перетаскивани `.show function`](show-function.md) |Перечисляет все хранимые функции или определенную функцию в текущей выбранной базе данных. |