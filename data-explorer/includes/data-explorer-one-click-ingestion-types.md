---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 30/03/2020
ms.author: orspodek
ms.openlocfilehash: 56095df921864896dacdb100302d686d66f40572
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82109727"
---
## <a name="select-an-ingestion-type"></a>Выберите тип приема данных

Для параметра **Ingestion type** (Тип приема данных) вы берите один из таких вариантов:
   * **Из хранилища**: в поле **Link to storage** (Ссылка на хранилище) добавьте URL-адрес учетной записи хранения. Для частных учетных записей хранения используйте [подписанный URL-адрес BLOB-объекта](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).
   
      ![Прием данных из хранилища одним щелчком](media/data-explorer-one-click-ingestion-types/from-storage-blob.png)

    * **Из файла**: нажмите кнопку **Обзор**, чтобы найти файл, или перетащите файл в поле.
  
      ![Прием данных из файла одним щелчком](media/data-explorer-one-click-ingestion-types/from-file.png)

    * **from container** (Из контейнера): в поле **Link to storage** (Ссылка на хранилище) добавьте [подписанный URL-адрес](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) контейнера и при необходимости укажите размер выборки.

      ![Прием данных из контейнера одним щелчком](media/data-explorer-one-click-ingestion-types/from-container.png)

  Появится пример данных. При необходимости можно отфильтровать эти данные, чтобы отображались только те файлы, имена которых в начале или в конце имеют определенные символы. Когда вы меняете фильтры, содержимое в окне предварительного просмотра обновляется автоматически.
  
  Например, можно выполнить фильтрацию по всем файлам, имена которых начинаются со слова *data* и заканчиваются расширением *.csv.gz*.

  ![Фильтрация для функции приема данных одним щелчком](media/data-explorer-one-click-ingestion-types/from-container-with-filter.png)
