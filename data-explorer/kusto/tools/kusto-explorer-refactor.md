---
title: Рефакторинг кода обозревателя Kusto — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается рефакторинг кода Kusto Explorer в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/05/2019
ms.openlocfilehash: 959cf8d25b20d459b48a0c8f1968541b50917a9d
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942239"
---
# <a name="kusto-explorer-code-refactoring"></a>Рефакторинг кода обозревателя Kusto

Как и в случае с другими IDE, Kusto. Explorer предлагает несколько функций для ККЛ редактирования и рефакторинга запросов.

## <a name="rename-variable-or-column-name"></a>Переименовать переменную или имя столбца

`Ctrl` + `R` Если щелкнуть, `Ctrl` + `R` в окне редактора запросов можно будет переименовать текущий выбранный символ.

См. снимок, демонстрирующий работу:

![Анимированный GIF, отображающий переменную, переименованную в окне редактора запросов. Три вхождения одновременно заменяются новым именем.](./Images/KustoTools-KustoExplorer/ke-refactor-rename.gif "Рефакторинг — переименование")

## <a name="extract-scalars-as-let-expressions"></a>Извлечение скаляров в виде `let` выражений

Вы можете повысить уровень выбранного литерала в качестве `let` выражения, нажав кнопку `Alt` + `Ctrl` + `M` . 

![Анимированный GIF. Указатель редактора запросов начинается с литерального выражения. После этого появляется инструкция let, которая устанавливает это литеральное значение в новую переменную.](./Images/KustoTools-KustoExplorer/ke-extract-as-let-literal.gif "Извлечение в качестве Let-литерала")

## <a name="extract-tabular-statements-as-let-expressions"></a>Извлечение табличных инструкций в виде `let` выражений

Можно также преобразовать табличные выражения в `let` инструкции, выбрав его текст и нажав кнопку `Alt` + `Ctrl` + `M` . 

![Анимированный GIF. Табличное выражение выбирается в редакторе запросов. После этого появляется инструкция let, которая устанавливает табличное выражение в новую переменную.](./Images/KustoTools-KustoExplorer/ke-extract-as-let-tabular.gif "Извлечение как Let-табличное")
