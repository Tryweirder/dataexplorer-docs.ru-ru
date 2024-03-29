---
title: Использование приема данных одним щелчком для приема данных в формате CSV из контейнера в новую таблицу в Azure Data Explorer
description: Прием (загрузка) данных в новую таблицу Azure Data Explorer одним щелчком.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/29/2020
ms.openlocfilehash: 3ac9788eda7a75173778ce0533f59820cfd7e7dd
ms.sourcegitcommit: abbcb27396c6d903b608e7b19edee9e7517877bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100528280"
---
# <a name="use-one-click-ingestion-to-ingest-csv-data-from-a-container-to-a-new-table-in-azure-data-explorer"></a>Использование приема данных одним щелчком для приема данных в формате CSV из контейнера в новую таблицу в Azure Data Explorer

> [!div class="op_single_selector"]
> * [Прием данных в формате CSV из контейнера в новую таблицу](one-click-ingestion-new-table.md)
> * [Прием данных в формате JSON из локального файла в существующую таблицу](one-click-ingestion-existing-table.md)

[Функция приема одним щелчком](ingest-data-one-click.md) позволяет быстро принимать данные в формате JSON, CSV и других форматах в таблицу, а также без усилий создавать структуры сопоставления. Данные можно принимать из хранилища, локального файла или контейнера в рамках однократного или непрерывного приема.  

В этом документе описывается применение интуитивно понятного мастера выполнения операций одним щелчком в сценарии приема данных в формате **CSV** из **контейнера** в **новую таблицу**. После приема вы можете [настроить конвейер приема Сетки событий](#create-continuous-ingestion-for-container), который выполняет прослушку на наличие новых файлов в исходном контейнере и принимает удовлетворяющие требованиям данные в новую таблицу. Этот же процесс можно запускать с незначительными изменениями и для других вариантов использования.

Общие сведения о приеме одним щелчком и список предварительных условий см. в [этой статье](ingest-data-one-click.md).
Сведения о приеме данных в имеющуюся таблицу в Azure Data Explorer см. в [этой статье](one-click-ingestion-existing-table.md).

## <a name="ingest-new-data"></a>Прием новых данных

1. В меню веб-интерфейса пользователя слева щелкните правой кнопкой мыши *базу данных* и выберите **Ingest new data** (Прием новых данных).

    :::image type="content" source="media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png" alt-text="Прием новых данных":::

1. В окне **Ingest new data** (Прием новых данных) выберите вкладку **Источник**. Поля **кластера** и **базы данных** заполняются автоматически.

    [!INCLUDE [one-click-cluster](includes/one-click-cluster.md)]

1. Выберите элементы **Таблица** > **Создать новую** и введите имя новой таблицы. Вы можете использовать буквенно-цифровые символы, дефисы и символы подчеркивания. Специальные символы не поддерживаются.

    > [!NOTE]
    > Имена таблиц должны включать от 1 до 1024 символов.

    :::image type="content" source="media/one-click-ingestion-new-table/create-new-table.png" alt-text="Создание новой таблицы при приеме данных одним щелчком":::

## <a name="select-an-ingestion-type"></a>Выберите тип приема данных

В разделе **Тип источника** сделайте следующее:
   
  1. Выберите элемент **From blob container** (Из контейнера больших двоичных объектов). Это может быть контейнер больших двоичных объектов, контейнер ADLS 1-го поколения и контейнер ADLS 2-го поколения. Вы можете принять до 1000 больших двоичных объектов из одного контейнера.
  1. В поле **Link to storage** (Ссылка на хранилище) добавьте [подписанный URL-адрес](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) контейнера и при необходимости введите размер выборки. Ознакомьтесь со статьей [Прием данных из папки в контейнере](#ingest-from-folder-in-a-container), чтобы выполнить эту операцию.

      :::image type="content" source="media/one-click-ingestion-new-table/from-container.png" alt-text="Прием данных одним щелчком из контейнера":::

     > [!TIP] 
     > Сведения о приеме данных **из файла** см. в статье [Использование приема данных одним щелчком для приема данных в формате JSON из локального файла в существующую таблицу в Azure Data Explorer](one-click-ingestion-existing-table.md#select-an-ingestion-type).

### <a name="ingest-from-folder-in-a-container"></a>Прием данных из папки в контейнере

Для приема данных из определенной папки в контейнере [создайте строку в следующем формате](kusto/api/connection-strings/storage.md#azure-data-lake-store):

*container_path*`/`*folder_path*`;`*access_key_1*.

Вы будете использовать эту строку вместо URL-адреса SAS в поле для [выбора типа приема](#select-an-ingestion-type).

1. Перейдите к учетной записи хранения и последовательно выберите **Обозреватель службы хранилища > Выбрать контейнеры больших двоичных объектов**.

    :::image type="content" source="media/one-click-ingestion-new-table/blob-containers.png" alt-text="Снимок экрана: доступ к контейнерам больших двоичных объектов в учетной записи хранения Azure":::

1. Перейдите к выбранной папке и выберите **Копировать URL-адрес**. Вставьте это значение во временный файл и добавьте `;` в конец этой строки.

    :::image type="content" source="media/one-click-ingestion-new-table/copy-url.png" alt-text="Снимок экрана: копирование URL-адреса в папке в контейнере больших двоичных объектов в учетной записи хранения Azure":::

1. В меню слева в разделе **Параметры** выберите **Ключи доступа**.

    :::image type="content" source="media/one-click-ingestion-new-table/copy-key-1.png" alt-text="Снимок экрана: строка копирования ключей учетной записи хранения в разделе &quot;Ключи доступа&quot;":::

1. В разделе **key 1** скопируйте строку **ключа**. Вставьте это значение в конец строки из шага 2. 

### <a name="storage-subscription-error"></a>Ошибка подписки на хранилище

Ниже описаны действия, которые нужно выполнить, если при приеме данных из учетной записи хранения отобразится следующее сообщение об ошибке:

> Couldn't find the storage under your selected subscriptions. Please add the storage account *`storage_account_name`* subscription to your selected subscriptions in the portal (Не удалось найти хранилище для выбранных подписок. Добавьте учетную запись хранения *`storage_account_name`* к выбранным подпискам на портале).

1. Выберите значок :::image type="icon" source="media/ingest-data-one-click/directory-subscription-icon.png" border="false"::: в меню вверху справа. Откроется область **Каталог и подписка**.

1. В раскрывающемся списке **Все подписки** добавьте подписку своей учетной записи хранения в выбранный список. 

    :::image type="content" source="media/ingest-data-one-click/subscription-dropdown.png" alt-text="Снимок экрана: область &quot;Каталог и подписка&quot; с раскрывающимся списком подписок, выделенным красным.":::

## <a name="sample-data"></a>Пример данных

Появится пример данных. При необходимости можно отфильтровать данные, чтобы принимать только те файлы, имена которых в начале или в конце имеют определенные символы. Когда вы меняете фильтры, содержимое в окне предварительного просмотра обновляется автоматически.

Например, выполните фильтрацию по всем файлам, имена которых начинаются с определенного слова или оканчиваются расширением *.csv*.

:::image type="content" source="media/one-click-ingestion-new-table/from-container-with-filter.png" alt-text="Фильтрация для функции приема данных одним щелчком":::

Система случайным образом выберет один из файлов и создаст схему на основе этого **файла с определением схемы**. Вы можете выбрать другой файл.

## <a name="edit-the-schema"></a>Изменение схемы

Выберите **Изменение схемы**, чтобы просмотреть и изменить конфигурацию столбца таблицы.  Проанализировав имя источника, служба автоматически определяет, сжат он или нет.

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

[!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

> [!NOTE]
> При работе с табличными форматами вы не можете дважды сопоставить столбец. Чтобы сопоставить существующий столбец, сначала удалите новый столбец.

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>Начало приема данных

Выберите **Start ingestion** (Начать прием данных), чтобы создать таблицу и сопоставление и начать прием данных.

:::image type="content" source="media/one-click-ingestion-new-table/start-ingestion.png" alt-text="Запуск приема данных одним щелчком":::

## <a name="complete-data-ingestion"></a>Завершение приема данных

Если прием данных завершается успешно, в окне **Data ingestion completed** (Прием данных завершен) все три шага будут отмечены галочками зеленого цвета.

:::image type="content" source="media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png" alt-text="Прием данных одним щелчком завершен"::: 

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="create-continuous-ingestion-for-container"></a>Создание непрерывного приема данных для контейнера

Функция непрерывного приема позволяет создать Сетку событий, которая прослушивает исходный контейнер на наличие новых файлов. Любой новый файл, который соответствует предопределенным критериям (префикс, суффикс и т. д.), будет автоматически включен в целевую таблицу. 

1. На плитке **Непрерывный прием** выберите элемент **Сетка событий**, чтобы открыть портал Azure. Страница подключения к данным отображается с открытым соединителем данных сетки событий и уже введенными исходными и целевыми параметрами (контейнер источника, таблицы и сопоставления).
    
    :::image type="content" source="media/one-click-ingestion-new-table/continuous-button.png" alt-text="Кнопка для создания непрерывного приема":::

1. Выберите **Create** (Создать), чтобы создать подключение к данным, которое будет ожидать любых изменений, обновлений или появления новых данных в этом контейнере. 

    :::image type="content" source="media/one-click-ingestion-new-table/event-hub-create.png" alt-text="Создание подключения концентратора событий":::

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство. Запрос данных в пользовательском веб-интерфейсе Azure Data Explorer](web-query-data.md)
* [Написание запросов для Azure Data Explorer на языке запросов Kusto](write-queries.md)
