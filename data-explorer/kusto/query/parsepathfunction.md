---
title: parse_path () - Исследователь данных Azure Документы Майкрософт
description: Эта статья описывает parse_path () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 2267efb4e47a6d8e45733ad48dd9f7f4019c1fa8
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744659"
---
# <a name="parse_path"></a>parse_path()

Сравнивает путь `string` файла и [`dynamic`](./scalar-data-types/dynamic.md) возвращает объект, содержащий следующие части пути: Схема, RootPath, DirectoryPath, DirectoryName, FileName, FileName, Расширение, AlternateDataStreamName.
В дополнение к простым путям с обоими типами слэйсов, поддерживает пути со схемами (например, "file://..."), общие пути (например, "общие\\дисковые"...), длинные пути (например, "?Кс:..."),\\альтернативные потоки данных (например, "file1.exe:file2.exe")

**Синтаксис**

`parse_path(`*Путь*`)`

**Аргументы**

* *путь*: строка, представляющая путь файла.

**Возвращает**

Объект типа, `dynamic` который прививал компоненты пути, перечисленные выше.

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
|C: Темп-файли.txt|"Схема":"","RootPath":"C:","DirectoryPath":"C:\\темп","DirectoryName":"Темп","Filename":"filename":"file.txt","Расширение":"Txt","AlternateDataStreamName":"""
|темп-файли.txt|"Схема":"","RootPath":"","DirectoryPath":"Темп","DirectoryName":"Temp","FileyName":"Temp","Filename":"file.txt","Расширение":"txt","AlternateDataName":"""
|file://C:/temp/file.txt:some.exe|"Scheme":"файл","RootPath":"C:","DirectoryPath":"C:/temp","DirectoryName":"Темп","Filename":"filename":"file.txt","Расширение":"txt","AlternateDataStreamName":"some.exe"
|\\общие пользователи-пользователи-темп-файл.txt.gz|\\\\"Схема":"","RootPath":"","DirectoryPath":" Общие\\\\пользователи темп","DirectoryName":"Темп","Filename":"filename":"file.txt.gz","Расширение":"Gz","AlternateDataStreamName":""
|/usr/lib/temp/file.txt|"Схема":"","RootPath":"","DirectoryPath":"/usr/lib/temp","DirectoryName":"Temp","Filename":"filename":"file.txt","Расширение":"txt","AlternateDataStreamName":""
