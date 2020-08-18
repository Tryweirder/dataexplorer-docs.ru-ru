---
title: dynamic_to_json () — обозреватель данных Azure
description: В этой статье описывается dynamic_to_json () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 08/05/2020
ms.openlocfilehash: 010610eb8cf6727c752f04564e8b3d1f71a405ab
ms.sourcegitcommit: 31ebf208d6bfd901f825d048ea69c9bb3d8b87af
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2020
ms.locfileid: "88502205"
---
# <a name="dynamic_to_json"></a>dynamic_to_json ()

Преобразует `dynamic` входные данные в строковое представление.
Если входные данные являются контейнером свойств, то выходная строка выводит содержимое, упорядоченное по ключам, рекурсивно. В противном случае выходные данные похожи на `tostring` выходные данные функции.

## <a name="syntax"></a>Синтаксис

`dynamic_to_json(Expr)`

## <a name="arguments"></a>Аргументы

* *Expr*: `dynamic` input. Функция принимает один аргумент.

## <a name="returns"></a>Возвращаемое значение

Возвращает строковое представление `dynamic` входных данных. Если входные данные являются контейнером свойств, то выходная строка выводит содержимое, упорядоченное по ключам, рекурсивно.

## <a name="examples"></a>Примеры

Выражение:

  Let bag1 = dynamic_to_json (Dynamic ({' Y10 ':d инамического ({}), ' x8 ": Dynamic ({" C3 ": 1, 8": 5, "A4": 6}), 1 ": 114," a1 ": 12," B1 ": 2," C1 ": 3," A14 ": [15, 13, 18]})); Печать bag1
  
Результат:

"{" "A1" ": 12," "A14" ": [15, 13, 18]," "B1" ": 2," C1 "": 3, "" D1 "": 114, "" x8 "": {"" C3 "": 1, "" D8 "": 5, "" A4 "": 6}, "" Y10 "": {} } "

Выражение:

 Let bag2 = dynamic_to_json (динамический ({' x8 ': Dynamic ({"A4": 6, "C3": 1, 8 ": 5})," A14 ": [15, 13, 18]," C1 ": 3," B1 ": 2," Y10 ": Dynamic ({})," a1 ": 12, 1": 114})); Печать bag2
 
Результат:

{"A1": 12, "A14": [15, 13, 18], "B1": 2, "C1": 3, "D1": 114, "x8": {"A4": 6, "C3": 1, "D8": 5}, "Y10": {} }
