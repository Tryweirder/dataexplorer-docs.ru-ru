---
title: Использование приема данных одним щелчком для приема данных в формате JSON из локального файла в существующую таблицу в Azure Data Explorer
description: Легкий прием (загрузка) данных в существующую таблицу Azure Data Explorer с помощью обработки "одним щелчком".
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/29/2020
ms.openlocfilehash: b5b6c199c1b374caa40a07e277a06591edccdd59
ms.sourcegitcommit: abbcb27396c6d903b608e7b19edee9e7517877bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100528423"
---
# <a name="use-one-click-ingestion-to-ingest-json-data-from-a-local-file-to-an-existing-table-in-azure-data-explorer"></a>Использование приема данных одним щелчком для приема данных в формате JSON из локального файла в существующую таблицу в Azure Data Explorer


> [!div class="op_single_selector"]
> * [Прием данных в формате CSV из контейнера в новую таблицу](one-click-ingestion-new-table.md)
> * [Прием данных в формате JSON из локального файла в существующую таблицу](one-click-ingestion-existing-table.md)

[Функция приема одним щелчком](ingest-data-one-click.md) позволяет быстро принимать данные в формате JSON, CSV и других форматах в таблицу, а также без усилий создавать структуры сопоставления. Данные можно принимать из хранилища, локального файла или контейнера в рамках однократного или непрерывного приема.  

В этом документе описывается применение интуитивно понятного мастера с выполнением операций одним щелчком в сценарии приема данных в формате **JSON** из **локального файла** в **существующую таблицу**. Используйте этот же процесс с незначительными изменениями и для других вариантов использования.

Общие сведения о приеме одним щелчком и список предварительных условий см. в [этой статье](ingest-data-one-click.md).
Сведения о различных типах источников данных см. в статье [Использование приема данных одним щелчком для приема данных в формате CSV из контейнера в новую таблицу в Azure Data Explorer](one-click-ingestion-new-table.md).

## <a name="ingest-new-data"></a>Прием новых данных

Слева в меню веб-интерфейса щелкните правой кнопкой мыши *базу данных* или *таблицу* и выберите **Ingest new data** (Прием новых данных).

   :::image type="content" source="media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png" alt-text="Выбор функции приема данных одним щелчком в пользовательском веб-интерфейсе":::
 
## <a name="select-an-ingestion-type"></a>Выберите тип приема данных

1. В окне **Ingest new data** (Прием новых данных) выберите вкладку **Источник**.

1. Если поля **кластера** и **баз данных** не заполнены автоматически, выберите в раскрывающемся меню существующие имена кластера и базы данных.
    
    [!INCLUDE [one-click-cluster](includes/one-click-cluster.md)]

1. Если поле **Таблица** не заполнено автоматически, выберите имя существующей таблицы из раскрывающегося меню.

1. В разделе **Тип источника** сделайте следующее:

   1. Выберите **from file** (из файла).  
   1. Нажмите кнопку **Обзор**, чтобы найти файлы (максимум 10 файлов), или перетащите файл в поле. Файл, определяющий схему, можно выбрать с помощью синей звездочки.
    
      :::image type="content" source="media/one-click-ingestion-existing-table/from-file.png" alt-text="Прием данных одним щелчком из файла":::

## <a name="edit-the-schema"></a>Изменение схемы

Выберите **Изменение схемы**, чтобы просмотреть и изменить конфигурацию столбца таблицы. На вкладке **Схема** выполните следующие действия:

   * **Тип сжатия** будет автоматически выбран по имени исходного файла. В этом случае используется тип сжатия **JSON**.
        
   * Если вы выбрали **JSON**, также задайте для параметра **Nested levels** (Вложенные уровни) значение от 1 до 10. Уровни определяют разделение данных столбцов в таблице.

        :::image type="content" source="media/one-click-ingestion-existing-table/json-levels.png" alt-text="Выбор вложенных уровней":::
    
       > [!TIP]
       > Если вы хотите использовать файлы в формате **CSV**, см. статью [Использование приема данных одним щелчком для приема данных в формате CSV из контейнера в новую таблицу в Azure Data Explorer](one-click-ingestion-new-table.md#edit-the-schema).

### <a name="add-nested-json-data"></a>Добавление вложенных данных JSON 

Чтобы добавить столбцы из уровней JSON, которые отличаются от основных **вложенных уровней**, выбранных выше, сделайте следующее:

1. Щелкните стрелку рядом с именем любого столбца и выберите элемент **Создать столбец**.

    :::image type="content" source="media/one-click-ingestion-existing-table/new-column.png" alt-text="Снимок экрана: параметры добавления нового столбца, вкладка схемы при приеме одним щелчком, Azure Data Explorer":::

1. Введите новое **имя столбца** и выберите **тип столбца** из раскрывающегося меню.
1. В разделе **Источник** выберите элемент **Создать**.

    :::image type="content" source="media/one-click-ingestion-existing-table/create-new-source.png" alt-text="Снимок экрана: создание источника для добавления вложенных данных JSON при приеме одним щелчком, Azure Data Explorer":::

1. Укажите новый источник для этого столбца и нажмите кнопку **ОК**. Этот источник может относиться к любому уровню JSON.

    :::image type="content" source="media/one-click-ingestion-existing-table/name-new-source.png" alt-text="Снимок экрана: всплывающее окно для присвоения имени новому источнику данных для добавленного столбца, прием одним щелчком в Azure Data Explorer":::

1. Нажмите кнопку **создания**. Новый столбец будет добавлен в конец таблицы.

    :::image type="content" source="media/one-click-ingestion-existing-table/create-new-column.png" alt-text="Снимок экрана: создание столбца во время приема одним щелчком в Azure Data Explorer":::

### <a name="edit-the-table"></a>Изменение таблицы 

[!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

> [!NOTE]
> * При работе с табличными форматами вы не можете дважды сопоставить столбец. Чтобы сопоставить существующий столбец, сначала удалите новый столбец.
> * Нельзя изменить существующий тип столбца. При попытке выполнить сопоставление для столбца, имеющего другой формат, могут отобразиться пустые столбцы.

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>Начало приема данных

Выберите **Start ingestion** (Начать прием данных), чтобы создать таблицу и сопоставление и начать прием данных.

:::image type="content" source="media/one-click-ingestion-existing-table/start-ingestion.png" alt-text="Начало приема данных":::

## <a name="complete-data-ingestion"></a>Завершение приема данных

Если прием данных завершается успешно, в окне **Data ingestion completed** (Прием данных завершен) все три шага будут отмечены галочками зеленого цвета.

:::image type="content" source="media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png" alt-text="Прием данных одним щелчком завершен":::

> [!IMPORTANT]
> Чтобы настроить непрерывный прием данных из контейнера, см. статью [Использование приема данных одним щелчком для приема данных в формате CSV из контейнера в новую таблицу в Azure Data Explorer](one-click-ingestion-new-table.md#create-continuous-ingestion-for-container).

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство. Запрос данных в пользовательском веб-интерфейсе Azure Data Explorer](web-query-data.md)
* [Написание запросов для Azure Data Explorer на языке запросов Kusto](write-queries.md)
