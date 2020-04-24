---
title: Kusto WPA - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается Kusto WPA в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 2d8c5a9b11d8045897d8a9bd798e40ceb0f48b6a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503304"
---
# <a name="kusto-wpa"></a>Кусто WPA

Kusto WPA добавляет возможность выполнения и визуализации результатов запроса Kusto в Анализаторе производительности Windows (WPA). Она состоит из двух основных частей:

1. Инструмент запуска, который может принимать kusto.Explorer-общие запросы (через) `Share` &gt; `Query to Clipboard`и генерировать файлы запросов (),`.kpq`которые будут обработаны WPA.

1. Плагин WPA, который может "открыть" генерируемые файлы запроса ().`.kpq` Открытие такого файла автоматически отправляет запрос, указанный в файле, в кластер Kusto и загружая результаты, как если бы они пришли из "стандартного" файла ETL.

https://aka.ms/kustowpaПодробную информацию и информацию о загрузке см.