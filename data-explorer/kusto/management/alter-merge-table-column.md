---
title: столбец таблицы альтер-слияние - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны столбцы таблицы альтер-слияния в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 75d298f35a215af5da443f673278e4a252c24cc9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522395"
---
# <a name="alter-merge-table-column-docstrings"></a>столбец таблицы альтер-слияние-docstrings

Устанавливает `docstring` свойство одной или нескольких столбцов указанной таблицы. Столбцы, явно не установленные, **сохраняют** существующее значение для этого свойства, если у них есть одно.

Для изменения столбца таблицы-docstring, [см.](#alter-table-column-docstrings)

**Синтаксис**

`.alter-merge``table` *TableName* `column-docstring` *Col1* `:` `:` `,` *Col2* *Docstring1* *Docstring2*Col1 Docstring1 - Col2 Docstring2 ... `(``)`

**Пример** 

```
.alter-merge table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```

## <a name="alter-table-column-docstrings"></a>изменить столбец таблицы-docstrings

Устанавливает `docstring` свойство одной или нескольких столбцов указанной таблицы. Столбцы, которые явно не установлены, будут **удалены**из этого свойства.

**Синтаксис**

`.alter``table` *TableName* `column-docstring` *Col1* `:` `:` `,` *Col2* *Docstring1* *Docstring2*Col1 Docstring1 - Col2 Docstring2 ... `(``)`

**Пример** 

```
.alter table Table1 column-docstrings (Column1:"DocString1", Column2:"DocString2")
```