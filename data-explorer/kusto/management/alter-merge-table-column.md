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
ms.openlocfilehash: 7dd36181be1140d3960369b1c8a5284ed55e48f5
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616496"
---
# <a name="alter-merge-table-column-docstrings"></a>ALTER-Merge столбец таблицы — докстрингс

Задает `docstring` свойство для одного или нескольких столбцов указанной таблицы. Столбцы, не заданные явным образом, **сохраняют** свое существующее значение для этого свойства, если они есть.

Инструкции по изменению столбца таблицы — docstring см. [ниже](#alter-table-column-docstrings).

**Синтаксис**

`.alter-merge``table` *TableName* `:` *Docstring1* *Col1* `:` *Docstring2*col1 Docstring1 [`,` Col2 Docstring2]... *Col2* `column-docstring` `(``)`

**Пример** 

```kusto
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

## <a name="alter-table-column-docstrings"></a>изменение столбца таблицы — докстрингс

Задает `docstring` свойство для одного или нескольких столбцов указанной таблицы. Для столбцов, не заданных явно, это свойство будет **удалено**.

**Синтаксис**

`.alter``table` *TableName* `:` *Docstring1* *Col1* `:` *Docstring2*col1 Docstring1 [`,` Col2 Docstring2]... *Col2* `column-docstring` `(``)`

**Пример** 

```kusto
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```
