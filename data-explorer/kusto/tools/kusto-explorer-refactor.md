---
title: Рефакторинг кода Kusto Explorer - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается рефакторинг кода Kusto Explorer в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/05/2019
ms.openlocfilehash: 0a89cf9c648fc4811d56c22012cdb25d5505eb4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523993"
---
# <a name="kusto-explorer-code-refactoring"></a>Рефакторинг кода Kusto Explorer

Как и в других IDEs, Kusto.Explorer предлагает несколько функций для редактирования и рефакторинга запросов к запросу КЗЛ.

## <a name="rename-variable-or-column-name"></a>Переименовать переменную или имя столбца

Нажатие `Ctrl` + `R` `Ctrl` + `R` кнопки в окне редактора запросов позволит вам переименовать выбранный в настоящее время символ.

Смотрите ниже снимок, который демонстрирует опыт:

![альт текст](./Images/KustoTools-KustoExplorer/ke-refactor-rename.gif "рефакторное переименование")

## <a name="extract-scalars-as-let-expressions"></a>Извлекайте скаляры в качестве `let` выражений

Вы можете продвигать в настоящее время `Alt` + `Ctrl` + `M`выбранный буквальный как `let` выражение, нажав кнопку . 

![альт текст](./Images/KustoTools-KustoExplorer/ke-extract-as-let-literal.gif "экстракт-как-пусть-буквальный")

## <a name="extract-tabular-statements-as-let-expressions"></a>Извлекайте таблики как `let` выражения

Вы также можете продвигать таблетные выражения в виде `let` утверждений, выбрав его текст, а затем нажав кнопку `Alt` + `Ctrl` + `M`. 

![альт текст](./Images/KustoTools-KustoExplorer/ke-extract-as-let-tabular.gif "экстракт-как-пусть-табулярный")
