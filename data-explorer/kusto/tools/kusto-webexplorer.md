---
title: Kusto. просматривает обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается Kusto. обозреватель данных в Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: d53f12c4a0c4dd2bce669dbe004b8f325db27af5
ms.sourcegitcommit: 4986354cc1ba25c584e4f3c7eac7b5ff499f0cf1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84856387"
---
# <a name="kustowebexplorer"></a>Kusto.

Kusto. WebService — это веб-приложение, которое можно использовать для отправки запросов и управления командами в службу Kusto. Приложение размещается в https://dataexplorer.azure.com/ и является коротким, связанным с https://aka.ms/kwe .



Kusto. веб-проводник может также размещаться на других порталах в окне IFRAME в формате HTML.
(Например, это можно сделать с помощью [портал Azure](https://portal.azure.com).) Дополнительные сведения о том, как разместить и использовать редактор Монако, см. в статье [Монако IDE](../api/monaco/monaco-kusto.md) .

## <a name="connect-to-multiple-clusters"></a>Подключение к нескольким кластерам

Теперь можно подключать несколько кластеров и переключаться между базами данных и кластерами.
Средство предназначено для простого обнаружения кластера и базы данных, к которым вы подключены.

![Замещающий текст](./Images/KustoTools-WebExplorer/AddingCluster.gif "аддингклустер")

## <a name="recall-results"></a>Результаты отзыва

Часто во время анализа выполняется несколько запросов, и может потребоваться повторное посещение результатов предыдущих запросов. Эту функцию можно использовать для отзыва результатов без необходимости повторного выполнения запроса. Данные обслуживаются из локального кэша на стороне клиента.

![Замещающий текст](./Images/KustoTools-WebExplorer/RecallResults.gif "рекаллресултс")

## <a name="enhanced-results-grid-control"></a>Элемент управления таблицы улучшенных результатов

Сетка таблицы позволяет выбрать несколько строк, столбцов и ячеек. Вычисление агрегатов путем выбора нескольких ячеек (например, Excel) и сведение данных.

![Замещающий текст](./Images/KustoTools-WebExplorer/EnhancedGrid.gif "енханцедгрид")

## <a name="intellisense--formatting"></a>Форматирование & IntelliSense

Можно использовать формат для печати в удобном формате, используя сочетание клавиш SHIFT + ALT + F, свертывание кода (структурирование) и IntelliSense.

![Замещающий текст](./Images/KustoTools-WebExplorer/Formating.gif "Форматирование")

## <a name="deep-linking"></a>Глубокая компоновка

Можно скопировать только глубокую ссылку или глубокую ссылку и запрос. Можно также отформатировать URL-адрес для включения кластера, базы данных и запроса с помощью следующего шаблона:

`https://dataexplorer.azure.com/`[ `clusters/` *Cluster* [ `/databases/` *Database* `?` *Параметры*]]]

Можно указать следующие параметры:

* `workspace=empty`: Указывает на создание пустой рабочей области (без отзыва предыдущих кластеров, вкладок и запросов).



![Замещающий текст](./Images/KustoTools-WebExplorer/DeepLink.gif "DeepLink")

## <a name="feedback"></a>Отзывы

Вы можете отправить отзыв с помощью средства.
![Замещающий текст](./Images/KustoTools-WebExplorer/Feedback.gif "Отзывы")
