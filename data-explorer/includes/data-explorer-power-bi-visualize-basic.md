---
author: mgblythe
ms.service: data-explorer
ms.topic: include
ms.date: 11/14/2018
ms.author: mblythe
ms.openlocfilehash: 9624856841ec7473543575c31928c6eefd1404c1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501845"
---
Теперь у вас есть данные в Power BI Desktop, можно создавать отчеты на их основе. Вы создадите простой отчет с гистограммой, на которой показан урон посевам по штатам.

1. В левой части основного окна Power BI выберите представление отчетов.

    ![Представление отчетов](media/data-explorer-power-bi-visualize-basic/report-view.png)

1. В области **ВИЗУАЛИЗАЦИИ** выберите гистограмму с кластерами.

    ![Добавление гистограммы](media/data-explorer-power-bi-visualize-basic/add-column-chart.png)

    Пустая диаграмма добавляется на холст.

    ![Пустая диаграмма](media/data-explorer-power-bi-visualize-basic/blank-chart.png)

1. В списке **Поля** выберите **DamageCrops** и **State**.

    ![Выбор полей](media/data-explorer-power-bi-visualize-basic/select-fields.png)

    Теперь у вас есть диаграмма, показывающая урон посевам в первых 1000 строках в таблице.

    ![Урон посевам по штатам](media/data-explorer-power-bi-visualize-basic/damage-column-chart.png)

1. Сохраните отчет.