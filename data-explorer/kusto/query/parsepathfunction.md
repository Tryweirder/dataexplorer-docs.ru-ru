---
title: parse_path () — обозреватель данных Azure
description: В этой статье описывается parse_path () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 16b80c86f526cb05514577359603e9e21de80064
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224890"
---
# <a name="parse_path"></a>parse_path()

Анализирует путь к файлу `string` и возвращает [`dynamic`](./scalar-data-types/dynamic.md) объект, который содержит следующие части пути: схема, rootpath, DirectoryPath, Директоринаме, имя файла, расширение, алтернатедатастреамнаме.
Помимо простых путей с косыми чертами, поддерживает пути со схемами (например, "file://..."), общими путями (например " \\ шареддриве\усерс..."), длинными путями (например " \\ ? \c:..."), дополнительными потоками данных (например, "file1. exe: file2. exe").

**Синтаксис**

`parse_path(`*путь*`)`

**Аргументы**

* *path*: строка, представляющая путь к файлу.

**Возвращает**

Объект типа `dynamic` , включающий компоненты пути, перечисленные выше.

**Пример**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(p:string) 
[
    @"C:\temp\file.txt",
    @"temp\file.txt",
    "file://C:/temp/file.txt:some.exe",
    @"\\shared\users\temp\file.txt.gz",
    "/usr/lib/temp/file.txt"
]
| extend path_parts = parse_path(p)

```

|p|path_parts
|---|---
|к:\темп\филе.ткст|{"Схема": "", "RootPath": "C:", "DirectoryPath": "C: \\ TEMP", "директоринаме": "Temp", "filename": "файл. txt", "Extension": "txt", "алтернатедатастреамнаме": ""}
|темп\филе.ткст|{"Схема": "", "RootPath": "", "DirectoryPath": "Temp", "Директоринаме": "Temp", "имя_файла": "File. txt", "Extension": "txt", "Алтернатедатастреамнаме": ""}
|file://C:/temp/file.txt:some.exe|{"Схема": "File", "RootPath": "C:", "DirectoryPath": "C:/Temp", "Директоринаме": "Temp", "filename": "файл. txt", "Extension": "txt", "Алтернатедатастреамнаме": "часть. exe"}
|\\шаред\усерс\темп\филе.ткст.гз|{"Схема": "", "RootPath": "", "DirectoryPath": " \\ \\ Общие \\ Пользователи \\ TEMP "," директоринаме ":" Temp "," filename ":" файл. txt. gz "," Extension ":" GZ "," алтернатедатастреамнаме ":" "}
|/уср/либ/темп/филе.ткст|{"Схема": "", "RootPath": "", "DirectoryPath": "/УСР/либ/темп", "Директоринаме": "Temp", "имя_файла": "File. txt", "Extension": "txt", "Алтернатедатастреамнаме": ""}
