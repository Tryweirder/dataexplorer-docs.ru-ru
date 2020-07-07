---
title: ALTER-Merge столбец таблицы — докстрингс — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается инструкция ALTER-Merge таблицы Column-докстрингс в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f71a46e6c747b7f9d9f6a3ba2d2f8a308635128
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967219"
---
# <a name="alter-merge-table-column-docstrings"></a>.alter-merge table column-docstrings

Задает `docstring` свойство для одного или нескольких столбцов указанной таблицы. Столбцы, не заданные явным образом, **сохраняют** свое существующее значение для этого свойства, если они есть.

Инструкции по изменению столбца таблицы — docstring см. [ниже](#alter-table-column-docstrings).

**Синтаксис**

`.alter-merge``table` *TableName* `column-docstring` TableName `(` *Col1* `:` *Docstring1* [ `,` *Col2* `:` *Docstring2*]...`)`

**Пример** 

```kusto
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

## <a name="alter-table-column-docstrings"></a>изменение столбца таблицы — докстрингс

Задает `docstring` свойство для одного или нескольких столбцов указанной таблицы. Для столбцов, не заданных явно, это свойство будет **удалено**.

**Синтаксис**

`.alter``table` *TableName* `column-docstring` TableName `(` *Col1* `:` *Docstring1* [ `,` *Col2* `:` *Docstring2*]...`)`

**Пример** 

```kusto
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```
