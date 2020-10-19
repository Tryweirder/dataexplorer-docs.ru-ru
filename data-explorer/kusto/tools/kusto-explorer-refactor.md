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
ms.openlocfilehash: 2e73e10bd62f9767bfe578eadc747372cd604362
ms.sourcegitcommit: 88923cfb2495dbf10b62774ab2370b59681578b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92175663"
---
# <a name="kusto-explorer-code-refactoring"></a>Рефакторинг кода обозревателя Kusto

Как и в случае с другими IDE, Kusto. Explorer предлагает несколько функций для ККЛ редактирования и рефакторинга запросов.

## <a name="rename-variable-or-column-name"></a>Переименовать переменную или имя столбца

`Ctrl` + `R` Если щелкнуть, `Ctrl` + `R` в окне редактора запросов можно будет переименовать текущий выбранный символ.

См. снимок, демонстрирующий работу:

![Анимированный GIF, отображающий переменную, переименованную в окне редактора запросов. Три вхождения одновременно заменяются новым именем.](./Images/kusto-explorer-refactor/ke-refactor-rename.gif "Рефакторинг — переименование")

## <a name="extract-scalars-as-let-expressions"></a>Извлечение скаляров в виде `let` выражений

Вы можете повысить уровень выбранного литерала в качестве `let` выражения, нажав кнопку `Alt` + `Ctrl` + `M` . 

![Анимированный GIF. Указатель редактора запросов начинается с литерального выражения. После этого появляется инструкция let, которая устанавливает это литеральное значение в новую переменную.](./Images/kusto-explorer-refactor/ke-extract-as-let-literal.gif "Извлечение в качестве Let-литерала")

## <a name="extract-tabular-statements-as-let-expressions"></a>Извлечение табличных инструкций в виде `let` выражений

Можно также преобразовать табличные выражения в `let` инструкции, выбрав его текст и нажав кнопку `Alt` + `Ctrl` + `M` . 

![Анимированный GIF. Табличное выражение выбирается в редакторе запросов. После этого появляется инструкция let, которая устанавливает табличное выражение в новую переменную.](./Images/kusto-explorer-refactor/ke-extract-as-let-tabular.gif "Извлечение как Let-табличное")
