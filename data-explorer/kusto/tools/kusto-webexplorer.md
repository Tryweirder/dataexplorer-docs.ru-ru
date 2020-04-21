---
title: Kusto.WebExplorer - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается Kusto.WebExplorer в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 1f6926df09a207cfea2b9201ef57f36932a63f74
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523874"
---
# <a name="kustowebexplorer"></a>Kusto.WebExplorer

Kusto.WebExplorer — это веб-приложение, которое можно использовать для отправки запросов и команд управления в службу Kusto. Приложение размещаетсяhttps://dataexplorer.azure.com/по ссылке наhttps://aka.ms/kwe«К» и коротко связано с помощью .



Kusto.WebExplorer также может быть размещен на других веб-порталах в HTML IFRAME.
(Например, это делается [порталом Azure](https://portal.azure.com).) Подробную информацию о том, как его принимать у себя, читайте в материале [журнала "Огонек" "Монако".](../api/monaco/monaco-kusto.md)

## <a name="connect-to-multiple-clusters"></a>Подключение к нескольким кластерам

Теперь можно подключить несколько кластеров и переключаться между базами данных и кластерами.
Инструмент предназначен для легкой идентификации кластера и базы данных, к которой вы подключены.

![альт текст](./Images/KustoTools-WebExplorer/AddingCluster.gif "ДобавлениеКластера")

## <a name="recall-results"></a>Отзыв результатов

Часто во время анализа мы запускаем несколько запросов и, возможно, придется пересмотреть результаты предыдущих запросов. Эту функцию можно использовать для отзыва результатов без повторного выполнения запроса. Данные подаются из локального кэша на стороне клиента.

![альт текст](./Images/KustoTools-WebExplorer/RecallResults.gif "ОтзывРезультаты")

## <a name="enhanced-results-grid-control"></a>Улучшенный контроль сетки результатов

Таблица позволяет выбрать несколько строк, столбцов и ячеек. Вычислите агрегаты, выбрав несколько ячеек (например, Excel) и разобрав данные.

![альт текст](./Images/KustoTools-WebExplorer/EnhancedGrid.gif "EnhancedGrid")

## <a name="intellisense--formatting"></a>Интеллиsense & Форматирование

Вы можете использовать формат pretty-print, используя клавишу ярлыка "Shift и Alt s F", складывание кода (изложение) и IntelliSense.

![альт текст](./Images/KustoTools-WebExplorer/Formating.gif "Форматирования")

## <a name="deep-linking"></a>Глубокая связь

Вы можете скопировать только глубокую ссылку или глубокую ссылку и запрос. Можно также отформатировать URL-адрес для включения кластера, базы данных и запроса с помощью следующего шаблона:

`https://dataexplorer.azure.com/`Кластер`clusters/` *Cluster* -`/databases/` База`?` *данных* и *варианты*

Возможны следующие варианты:

* `workspace=empty`: Указывает на создание нового пустого рабочего пространства (не будет выполнен отзыв предыдущих кластеров, вкладок и запросов).



![альт текст](./Images/KustoTools-WebExplorer/DeepLink.gif "DeepLink")

## <a name="feedback"></a>Отзывы

Вы можете отправить свои отзывы с помощью инструмента.
![альт текст](./Images/KustoTools-WebExplorer/Feedback.gif "Отзывы")