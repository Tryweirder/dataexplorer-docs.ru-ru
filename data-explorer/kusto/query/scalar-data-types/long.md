---
title: Длинный тип данных - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается длинный тип данных в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b8c51f216b8515e483daf64d9783210ff35ceef3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509713"
---
# <a name="the-long-data-type"></a>Длинный тип данных

Тип `long` данных представляет собой подписанный, 64-битный широкий, ряд.

## <a name="long-literals"></a>длинные буквальные

Буквы типа `long` данных могут быть указаны в следующем синтаксисе:

`long``(` *Значение*`)`

Где *значение* может принимать следующие формы:
* Еще одна цифра или цифры, и в этом случае буквальное значение является десятичным представлением этих цифр. Например, `long(12)` это число двенадцать `long`типа .
* Префикс `0x` сопровождается одной или нескольких цифр hex. Например, предложение `long(0xf)` эквивалентно предложению `long(15)`.
* Минус ()`-`знак с последующим одним или более цифрами. Например, `long(-1)` это число минус `long`один типа .
* `null`, в этом случае это [нулевая стоимость](null-values.md) типа `long` данных. Таким образом, нулевая `long` `long(null)`стоимость типа .

Кусто также поддерживает буквальные `long` типа `long(` / `)` без префикса/суфии только для первых двух форм. Таким `123` `long`образом, является буквальном типа, как `0x123`есть, `-2` но **не** буквальное (в настоящее время интерпретируется как неокупная функция `-` применяется к `2` буквальному типа долго).
 
Для преобразования долго в гекс строки - см [tohex() функции](../tohexfunction.md).