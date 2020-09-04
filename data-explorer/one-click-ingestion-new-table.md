---
title: Использование приема данных одним щелчком для приема данных в формате CSV из контейнера в новую таблицу в Azure Data Explorer
description: Прием (загрузка) данных в новую таблицу Azure Data Explorer одним щелчком.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/29/2020
ms.openlocfilehash: 9427692d0533550967bfe84a35fd833a4c03b39e
ms.sourcegitcommit: 811cf98edefd919b412d80201400919eedcab5cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89274813"
---
# <a name="use-one-click-ingestion-to-ingest-csv-data-from-a-container-to-a-new-table-in-azure-data-explorer"></a>Использование приема данных одним щелчком для приема данных в формате CSV из контейнера в новую таблицу в Azure Data Explorer

> [!div class="op_single_selector"]
> * [Прием данных в формате CSV из контейнера в новую таблицу](one-click-ingestion-new-table.md)
> * [Прием данных в формате JSON из локального файла в существующую таблицу](one-click-ingestion-existing-table.md)

[Функция приема одним щелчком](ingest-data-one-click.md) позволяет быстро принимать данные в формате JSON, CSV и других форматах в таблицу, а также без усилий создавать структуры сопоставления. Данные можно принимать из хранилища, локального файла или контейнера в рамках однократного или непрерывного приема.  

В этом документе описывается применение интуитивно понятного мастера выполнения операций одним щелчком в сценарии приема данных в формате **CSV** из **контейнера** в **новую таблицу**. Этот же процесс можно запускать с незначительными изменениями и для других вариантов использования.

Общие сведения о приеме одним щелчком и список предварительных условий см. в [этой статье](ingest-data-one-click.md).
Сведения о приеме данных в имеющуюся таблицу в Azure Data Explorer см. в [этой статье](one-click-ingestion-existing-table.md).

## <a name="ingest-new-data"></a>Прием новых данных

1. В меню веб-интерфейса пользователя слева щелкните правой кнопкой мыши *базу данных* и выберите **Ingest new data (Preview)** (Прием новых данных (предварительная версия)).

    :::image type="content" source="media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png" alt-text="Прием новых данных":::

1. В окне **Ingest new data (Preview)** (Прием новых данных (предварительная версия)) выбирается вкладка **Источник**. 

1. Выберите **Создать таблицу** и введите имя новой таблицы. Вы можете использовать буквенно-цифровые символы, дефисы и символы подчеркивания. Специальные символы не поддерживаются.

    > [!NOTE]
    > Имена таблиц должны включать от 1 до 1024 символов.

    :::image type="content" source="media/one-click-ingestion-new-table/create-new-table.png" alt-text="Создание новой таблицы при приеме данных одним щелчком":::

## <a name="select-an-ingestion-type"></a>Выберите тип приема данных

В разделе **Ingestion type** (Тип приема) сделайте следующее:
   
  1. Выберите **from container** (из контейнера). 
  1. В поле **Link to storage** (Ссылка на хранилище) добавьте [подписанный URL-адрес](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) контейнера и при необходимости введите размер выборки.

      :::image type="content" source="media/one-click-ingestion-new-table/from-container.png" alt-text="Прием данных одним щелчком из контейнера":::

     > [!TIP] 
     > Сведения о приеме данных **из файла** см. в статье [Использование приема данных одним щелчком для приема данных в формате JSON из локального файла в существующую таблицу в Azure Data Explorer](one-click-ingestion-existing-table.md#select-an-ingestion-type).

Появится пример данных. При необходимости можно отфильтровать данные, чтобы принимать только те файлы, имена которых в начале или в конце имеют определенные символы. Когда вы меняете фильтры, содержимое в окне предварительного просмотра обновляется автоматически.

Например, выполните фильтрацию по всем файлам, имена которых начинаются с определенного слова или оканчиваются расширением *.csv*.

:::image type="content" source="media/one-click-ingestion-new-table/from-container-with-filter.png" alt-text="Фильтрация для функции приема данных одним щелчком":::
  
## <a name="edit-the-schema"></a>Изменение схемы

Выберите **Изменение схемы**, чтобы просмотреть и изменить конфигурацию столбца таблицы. Система случайным образом выберет один из больших двоичных объектов и создаст схему на основе этого объекта. Проанализировав имя источника, служба автоматически определяет, сжат он или нет.

На вкладке **Схема** выполните следующие действия:

   1. Выберите **Формат данных**:

        В этом случае используется формат данных **CSV**.

        > [!TIP]
        > Если вы хотите использовать файлы в формате **JSON**, см. статью [Использование приема данных одним щелчком для приема данных в формате JSON из локального файла в существующую таблицу в Azure Data Explorer](one-click-ingestion-existing-table.md#edit-the-schema).

   1. Вы можете установить флажок **Include column names** (Включить имена столбцов), чтобы не учитывать строку заголовка файла.

        :::image type="content" source="media/one-click-ingestion-new-table/non-json-format.png" alt-text="Установка флажка Include column names (Включить имена столбцов)":::

В поле **Mapping name** (Имя сопоставления) укажите имя сопоставления. Вы можете использовать буквенно-цифровые символы и символы подчеркивания. Пробелы, специальные символы и дефисы не поддерживаются.

:::image type="content" source="media/one-click-ingestion-new-table/table-mapping.png" alt-text="Имя сопоставления таблицы при приеме данных одним щелчком":::

### <a name="edit-the-table"></a>Изменение таблицы

Чтобы реализовать прием данных в новую таблицу, нужно изменить некоторые аспекты таблицы при ее создании.

В таблице выполните следующие действия: 
 * Дважды щелкните имя нового столбца, чтобы изменить его.
 * Выберите новые заголовки столбцов и выполните любое из следующих действий:

    [!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

  > [!NOTE]
  > Табличные форматы поддерживают прием столбцов в один столбец в Azure Data Explorer.

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>Начало приема данных

Выберите **Start ingestion** (Начать прием данных), чтобы создать таблицу и сопоставление и начать прием данных.

:::image type="content" source="media/one-click-ingestion-new-table/start-ingestion.png" alt-text="Запуск приема данных одним щелчком":::

## <a name="complete-data-ingestion"></a>Завершение приема данных

Если прием данных завершается успешно, в окне **Data ingestion completed** (Прием данных завершен) все три шага будут отмечены галочками зеленого цвета.

:::image type="content" source="media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png" alt-text="Прием данных одним щелчком завершен"::: 

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="create-continuous-ingestion-for-container"></a>Создание непрерывного приема данных для контейнера

Функция непрерывного приема позволяет создать сетку событий, которая прослушивает исходный контейнер на наличие новых файлов. Любой новый файл, который соответствует предопределенным критериям (префикс, суффикс и т. д.), будет автоматически включен в целевую таблицу. 

1. На плитке **Непрерывный прием** выберите элемент **Сетка событий**, чтобы открыть портал Azure. Страница подключения к данным отображается с открытым соединителем данных сетки событий и уже введенными исходными и целевыми параметрами (контейнер источника, таблицы и сопоставления).
    
    :::image type="content" source="media/one-click-ingestion-new-table/continuous-button.png" alt-text="Кнопка для создания непрерывного приема":::

1. Выберите **Create** (Создать), чтобы создать подключение к данным, которое будет ожидать любых изменений, обновлений или появления новых данных в этом контейнере. 

    :::image type="content" source="media/one-click-ingestion-new-table/event-hub-create.png" alt-text="Создание подключения концентратора событий":::

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство. Запрос данных в пользовательском веб-интерфейсе Azure Data Explorer](web-query-data.md)
* [Написание запросов для Azure Data Explorer на языке запросов Kusto](write-queries.md)
