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
ms.openlocfilehash: ba74b7c1e78d568cc34845d56dc9768f2628192f
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717382"
---
# <a name="parse_path"></a>parse_path()

Анализирует путь к файлу `string` и возвращает [`dynamic`](./scalar-data-types/dynamic.md) объект, содержащий следующие части пути:
* Схема
* RootPath
* DirectoryPath
* DirectoryName
* FileName
* Расширение
* алтернатедатастреамнаме

Помимо простых путей с косыми чертами, функция поддерживает пути с:
* Схемы. Например, "file://..."
* Общие пути. Например, " \\ шареддриве\усерс..."
* Длинные пути. Например, " \\ ? \c:..."
* Альтернативные потоки данных. Например, "file1.exe:file2.exe"

**Синтаксис**

`parse_path(`*path*`)`

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
|C:\temp\file.txt|{"Схема": "", "RootPath": "C:", "DirectoryPath": "C: \\ TEMP", "директоринаме": "Temp", "имя_файла": "file.txt", "Extension": "txt", "алтернатедатастреамнаме": ""}
|temp\file.txt|{"Схема": "", "RootPath": "", "DirectoryPath": "Temp", "Директоринаме": "Temp", "имя_файла": "file.txt", "Extension": "txt", "Алтернатедатастреамнаме": ""}
|file://C:/temp/file.txt:some.exe|{"Схема": "File", "RootPath": "C:", "DirectoryPath": "C:/Temp", "Директоринаме": "Temp", "имя_файла": "file.txt", "Extension": "txt", "Алтернатедатастреамнаме": "some.exe"}
|\\shared\users\temp\file.txt. gz|{"Схема": "", "RootPath": "", "DirectoryPath": " \\ \\ Общие \\ Пользователи \\ TEMP "," директоринаме ":" Temp "," имя_файла ":" file.txt. gz "," Extension ":" GZ "," алтернатедатастреамнаме ":" "}
|/УСР/либ/темп/file.txt|{"Схема": "", "RootPath": "", "DirectoryPath": "/УСР/либ/темп", "Директоринаме": "Temp", "имя_файла": "file.txt", "Extension": "txt", "Алтернатедатастреамнаме": ""}
