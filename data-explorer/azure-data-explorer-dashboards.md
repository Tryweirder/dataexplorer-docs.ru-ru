---
title: Визуализация данных с помощью панели мониторинга обозреватель данных Azure
description: Сведения о визуализации данных с помощью панели мониторинга обозреватель данных Azure
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: 46e3821514ca5b06852f7e8428b12cf9f80e29a4
ms.sourcegitcommit: a562ce255ac706ca1ca77d272a97b5975235729d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867876"
---
# <a name="visualize-data-with-azure-data-explorer-dashboards"></a>Визуализация данных с помощью панелей мониторинга обозреватель данных Azure

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Обозреватель данных Azure предоставляет веб-приложение, которое позволяет выполнять запросы и создавать панели мониторинга. Панели мониторинга доступны в изолированном веб-приложении, [веб-интерфейсе](web-query-data.md). Обозреватель данных Azure также интегрируется с другими службами панелей мониторинга, такими как [Power BI](power-bi-connector.md) и [Grafana](grafana.md).

Панели мониторинга обозреватель данных Azure предоставляют три основных преимущества:

* Экспорт запросов из веб-интерфейса в Azure обозреватель данных панели мониторинга. 
* Изучите данные в пользовательском веб-интерфейсе.
* Оптимизированная производительность отрисовки панели мониторинга.

На следующем рисунке показана панель мониторинга обозреватель данных Azure.

:::image type="content" source="media/adx-dashboards/dash.png" alt-text="Итоговая панель мониторинга":::

## <a name="create-a-dashboard"></a>Создание панели мониторинга

1. На панели навигации выберите **панели мониторинга** и щелкните **Новая панель мониторинга**.

    :::image type="content" source="media/adx-dashboards/new-dashboard.png" alt-text="Новая панель мониторинга":::

1. Выберите имя панели мониторинга и **Создайте**.

    :::image type="content" source="media/adx-dashboards/new-dashboard-popup.png" alt-text="Создание панели мониторинга":::

## <a name="add-data-source"></a>Добавление источника данных

Добавьте необходимые источники данных для панелей мониторинга.

1. Выберите пункт меню **Источники данных** на верхней панели. Нажмите кнопку **+ создать источник данных** на панели **Источники данных** .

    :::image type="content" source="media/adx-dashboards/data-source.png" alt-text="Источник данных":::

1. В области **создать новый источник данных** выполните следующие действия.
    1. Введите **URI кластера** или частичное имя, включая регион, и выберите **подключить**. 
    1. Выберите из раскрывающегося списка **базу данных** для восстановления.
    1. Используйте значение по умолчанию или измените **имя источника данных**, если это необходимо. 
    1. Нажмите кнопку **Применить**.

    :::image type="content" source="media/adx-dashboards/data-source-pane.png" alt-text="Панель источника данных":::

## <a name="use-parameters"></a>Использование параметров

Параметры позволяют использовать фильтры панели мониторинга. Параметры значительно улучшают производительность отрисовки панели мониторинга и позволяют использовать значения фильтров как можно раньше в запросе.

1. Выберите **Параметры** на верхней панели. Нажмите кнопку **+ новый параметр** в области **Параметры** .

    :::image type="content" source="media/adx-dashboards/parameters.png" alt-text="Выберите новый параметр":::

1. Введите значения для всех обязательных полей и нажмите кнопку **Готово**.

    :::image type="content" source="media/adx-dashboards/parameter-pane.png" alt-text="Панель параметров":::

|Поле  |Описание |
|---------|---------|
|**Отображаемое имя параметра**    |   Имя параметра, отображаемого на панели мониторинга или в карточке редактирования.      |
|**Тип параметра**    |Это может быть:<ul><li>**Один выбор**: в фильтре в качестве входных данных для параметра можно выбрать только одно значение.</li><li>**Выбор нескольких элементов**. в качестве входных данных для параметра можно выбрать одно или несколько значений в фильтре.</li><li>**Диапазон времени**: позволяет создавать дополнительные параметры для фильтрации запросов и панелей мониторинга на основе времени. По умолчанию у каждой панели мониторинга есть выбор диапазона времени.</li></ul>    |
|**Имя переменной**     |   Имя параметра, который будет использоваться в запросе.      |
|**Data type**    |    Тип данных значений параметров.     |
|**Закрепить как фильтр панели мониторинга**   |   Закрепите фильтр на основе параметров на панели мониторинга или открепите его от панели мониторинга.       |
|**Источник**     |    Источник значений параметра: <ul><li>**Исправленные значения**. Вы вручную предоставили статические значения фильтра. </li><li>**Запрос**: динамически введенные значения с помощью запроса ККЛ.  </li></ul>    |
|**Добавить значение "выделить все"**    |   Применимо только к одиночному выделению и нескольким типам параметров выбора. Используется для получения данных для всех значений параметров.      |

## <a name="add-query"></a>Добавить запрос

При **добавлении запроса** используются фрагменты языка запросов Kusto для получения данных и отрисовки визуальных элементов. Каждый запрос может поддерживать один визуальный элемент.

1. Выберите **Добавить запрос** на холсте панели мониторинга или в верхней строке меню.

    :::image type="content" source="media/adx-dashboards/empty-dashboard-new-query.png" alt-text="Создать запрос":::

1. На панели **запросов** 
    1. Выберите источник данных из раскрывающегося списка
    1. Введите запрос и выберите **выполнить** . 
    1. Выберите **+ добавить визуальный** элемент

    :::image type="content" source="media/adx-dashboards/initial-query.png" alt-text="Выполнение запроса":::

1. В области **визуальное форматирование** выберите **тип диаграммы** , чтобы выбрать тип визуального элемента. 
1. Присвойте визуальному элементу имя и выберите **Применить изменения** , чтобы закрепить визуальный элемент на панели мониторинга.

    :::image type="content" source="media/adx-dashboards/add-visual.png" alt-text="Добавить визуальный элемент в запрос":::

1. Вы можете изменить размер визуального элемента и **сохранить изменения** , чтобы сохранить панель мониторинга.

    :::image type="content" source="media/adx-dashboards/save-dashboard.png" alt-text="сохранить панель мониторинга":::

## <a name="next-steps"></a>Next Steps

* [Запрос данных в обозреватель данных Azure](web-query-data.md)