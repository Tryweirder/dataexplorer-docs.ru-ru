---
title: Визуализация данных из Azure обозреватель данных с помощью Kibana
description: Из этой статьи вы узнаете, как настроить обозреватель данных Azure в качестве источника данных для Kibana.
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: bf479a7248033d2aa70a8e09b039814361c78031
ms.sourcegitcommit: bcd0c96b1581e43e33aa35f4d68af6dcb4979d39
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88039239"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Визуализация данных из Azure обозреватель данных в Kibana с помощью соединителя K2Bridge с открытым кодом

K2Bridge (Kibana-Kusto Bridge) позволяет использовать обозреватель данных Azure в качестве источника данных и визуализировать эти данные в Kibana. K2Bridge — это контейнерное приложение с [открытым кодом](https://github.com/microsoft/K2Bridge). Он выступает в качестве прокси-сервера между экземпляром Kibana и кластером Azure обозреватель данных. В этой статье описывается, как создать это подключение с помощью K2Bridge.

K2Bridge преобразует запросы Kibana к языку запросов Kusto (ККЛ) и отправляет результаты обозреватель данных Azure обратно в Kibana.

   ![Подключение Kibana к Azure обозреватель данных через K2Bridge](media/k2bridge/k2bridge-chart.png)

K2Bridge поддерживает вкладку **обнаружения** Kibana, где можно:

* Поиск и просмотр данных.
* Фильтрация результатов.
* Добавление или удаление полей в сетке результатов.
* Просмотр содержимого записи.
* Сохранение и совместное использование поиска.

На следующем рисунке показан экземпляр Kibana, привязанный к Azure обозреватель данных by K2Bridge. Взаимодействие с пользователем в Kibana не изменилось.

   [![Страница Kibana, привязанная к Azure обозреватель данных](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Предварительные требования

Прежде чем можно будет визуализировать данные из Azure обозреватель данных в Kibana, выполните следующие действия:

* [Helm v3](https://github.com/helm/helm#install)— диспетчер пакетов Kubernetes.

* Кластер Azure Kubernetes Service (AKS) или любой другой кластер Kubernetes. Версии 1,14 до 1,16 были протестированы и проверены. Если вам нужен кластер AKS, см. раздел Развертывание кластера AKS [с помощью Azure CLI](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) или [с помощью портал Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal).

* [Кластер Azure обозреватель данных](create-cluster-database-portal.md), включая URL-адрес кластера и имя базы данных.

* Субъект-служба Azure Active Directory (Azure AD), которая разрешает просматривать данные в обозреватель данных Azure, включая идентификатор клиента и секрет клиента.

    Рекомендуется использовать субъект-службу с разрешением на просмотр и запретить использование разрешений более высокого уровня. [Задайте разрешения на просмотр кластера для субъекта-службы Azure AD](manage-database-permissions.md#manage-permissions-in-the-azure-portal).

    Дополнительные сведения о субъекте-службе Azure AD см. в статье [Создание субъекта-службы Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Запуск K2Bridge в службе Kubernetes Azure (AKS)

По умолчанию Helm диаграмма K2Bridge ссылается на общедоступный образ, расположенный в реестре контейнеров Microsoft (мкр). МКР не требует никаких учетных данных.

1. Скачайте необходимые диаграммы Helm.

1. Добавьте зависимость Elasticsearch в Helm. Зависимость является обязательной, так как K2Bridge использует небольшой внутренний экземпляр Elasticsearch. Запросы, связанные с метаданными служб экземпляра, такие как запросы с шаблонами индексов и сохраненные запросы. Этот внутренний экземпляр не сохраняет бизнес-данные. Можно считать, что экземпляр является подробным описанием реализации.

    1. Чтобы добавить зависимость Elasticsearch в Helm, выполните следующие команды:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Чтобы получить диаграмму K2Bridge из каталога "диаграммы" репозитория GitHub, сделайте следующее:

        1. Клонировать репозиторий из [GitHub](https://github.com/microsoft/K2Bridge).
        1. Перейдите в каталог корневого репозитория K2Bridges.
        1. Выполните следующую команду:

            ```bash
            helm dependency update charts/k2bridge
            ```

1. Разверните K2Bridge.

    1. Задайте для переменных правильные значения для вашей среды.

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. При необходимости включите Application Insights телеметрии. Если вы используете Application Insights в первый раз, [создайте Application Insights ресурс](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource). [Скопируйте ключ инструментирования](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) в переменную.

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>Установите диаграмму K2Bridge.

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        В [конфигурации](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md)можно найти полный набор параметров конфигурации.

    1. <a name="install-kibana-service"></a>Выходные данные предыдущей команды предлагают следующую команду Helm для развертывания Kibana. При необходимости выполните следующую команду:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```

    1. Используйте перенаправление портов для доступа к Kibana на localhost.

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```

    1. Подключитесь к Kibana, перейдя по http://127.0.0.1:5601 .

    1. Предоставление пользователям Kibana. Это можно сделать несколькими способами. Метод, который вы используете во многом, зависит от вашего варианта использования.

        Например, службу можно предоставить в качестве службы Load Balancer. Для этого добавьте параметр **--Set Service. Type =** балансировщика в [предыдущую команду Kibana Helm **Install** ](#install-kibana-service).

        Затем выполните следующую команду:

        ```bash
        kubectl get service -w -n k2bridge
        ```

        Выходные данные должны выглядеть так:

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx    <pending>     5601:30128/TCP   4m24s
        ```

        Затем можно будет использовать созданное значение внешнего IP-адреса. Используйте его для доступа к Kibana, открыв браузер и перейдя по \<EXTERNAL-IP\> адресу: 5601.

1. Настройте шаблоны индексов для доступа к данным.

    В новом экземпляре Kibana:

    1. Откройте Kibana.
    1. Перейдите к **управлению**.
    1. Выберите **шаблоны индексов**.
    1. Создание шаблона индекса. Имя индекса должно точно совпадать с именем таблицы или функции без звездочки ( \* ). Вы можете скопировать соответствующую строку из списка.

> [!Note]
> Чтобы запустить K2Bridge для других поставщиков Kubernetes, измените значение Elasticsearch **сторажекласснаме** в поле Values. YAML, чтобы оно совпадало с предложенным поставщиком.

## <a name="visualize-data"></a>Визуализируйте данные

Когда обозреватель данных Azure настроен в качестве источника данных для Kibana, можно использовать Kibana для просмотра данных.

1. В Kibana в крайнем левом меню выберите вкладку **Обнаружение** .

1. В крайнем левом раскрывающемся списке выберите шаблон индекса. Шаблон определяет источник данных, который необходимо исследовать. В этом случае шаблон индекса является таблицей Azure обозреватель данных.

   ![Выбор шаблона индекса](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Если в данных есть поле фильтра времени, можно указать диапазон времени. В правом верхнем углу страницы **Обнаружение** выберите фильтр времени. По умолчанию на странице отображаются данные за последние 15 минут.

   ![Выбор фильтра времени](media/k2bridge/k2bridge-time-filter.png)

1. В таблице результатов показаны первые 500 записей. Можно развернуть документ, чтобы проверить данные его полей в формате JSON или таблицы.

   ![Развернутая запись](media/k2bridge/k2bridge-expand-record.png)

1. По умолчанию таблица результатов включает столбец **_source** . Он также включает столбец **времени** , если поле времени существует. Вы можете добавить определенные столбцы в таблицу результатов, нажав кнопку **Добавить** рядом с именем поля в левой области.

   ![Определенные столбцы с выделенной кнопкой "Добавить"](media/k2bridge/k2bridge-specific-columns.png)

1. В строке запроса можно выполнять поиск по следующим данным:

    * Ввод условия поиска.
    * Использование синтаксиса запроса Lucene. Пример:
        * Выполните поиск по слову "ошибка", чтобы найти все записи, содержащие это значение.
        * Выполните поиск по запросу "Status: 200", чтобы получить все записи со значением состояния 200.
    * Использование логических операторов **and**, **or**и **Not**.
    * Использование \* подстановочных знаков () и вопросительного знака (?). Например, запрос "destination_city: L *" соответствует записям, в которых значение Destination-City начинается с "L" или "l". (В K2Bridge не учитывается регистр.)

    ![Выполнение запроса](media/k2bridge/k2bridge-run-query.png)

    > [!Tip]
    > При [поиске](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)можно найти дополнительные правила и логику поиска.

1. Чтобы отфильтровать результаты поиска, используйте список полей на самой правой панели страницы. В списке полей можно увидеть следующее:

    * Пять основных значений поля.
    * Число записей, содержащих поле.
    * Процент записей, содержащих каждое значение.

    >[!Tip]
    > Используйте увеличительное стекло, чтобы найти все записи, имеющие определенное значение.

    ![Список полей с выделенным увеличительным стеклом](media/k2bridge/k2bridge-field-list.png)

    Можно также использовать увеличительное стекло для фильтрации результатов и просмотра представления в виде таблицы результатов каждой записи в таблице результатов.

     ![Список таблиц с выделенным увеличительным стеклом](media/k2bridge/k2bridge-table-list.png)

1. Выберите **сохранить** или **поделиться** для поиска.

     ![Выделенные кнопки для сохранения или совместного использования поиска](media/k2bridge/k2bridge-save-search.png)
