---
title: Устранение проблем доступа, приема и функционирования кластера Azure Data Explorer в виртуальной сети
description: Подключение, проглатывание, создание кластера и функционирование кластера Azure Data Explorer в виртуальной сети
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: b50b971a3b1980ad35a1a939bdf25f1c9e6ac7ba
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500766"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>Устранение проблем доступа, приема и функционирования кластера Azure Data Explorer в виртуальной сети

В этом разделе вы узнаете, как устранить проблемы подключения, оперативной работы и создания кластера для кластера, развернутого в [вашей виртуальной сети.](/azure/virtual-network/virtual-networks-overview)

## <a name="access-issues"></a>Проблемы доступа

Если у вас возникла проблема при доступе к кластеру с использованием публичной (cluster.region.kusto.windows.net) или частной (private-cluster.region.kusto.windows.net) конечная точка, и вы подозреваете, что она связана с виртуальной настройкой сети, выполните следующие шаги для устранения неполадок.

### <a name="check-tcp-connectivity"></a>Проверка подключения TCP

Первый шаг включает проверку подключения TCP с помощью Windows или Linux OS.

# <a name="windows"></a>[Windows](#tab/windows)

   1. Скачать [TCping](https://www.elifulkerson.com/projects/tcping.php) на машину, соединяющую с кластером.
   2. Пинг назначения от исходной машины с помощью следующей команды:

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. Установка *netcat* в машине, соединяющейся с кластером

    ```bash
    $ apt-get install netcat
     ```

   2. Пинг назначения от исходной машины с помощью следующей команды:

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

Если тест не удался, приступайте к следующим шагам. Если тест успешно, проблема не из-за проблемы подключения TCP. Перейдите к [оперативным вопросам](#cluster-creation-and-operations-issues) для устранения неполадок дальше.

### <a name="check-the-network-security-group-nsg"></a>Проверьте Группу сетевой безопасности (NSG)

   Убедитесь, что [группа сетевой безопасности](/azure/virtual-network/security-overview) (NSG), прикрепленная к подсети кластера, имеет входяво правило, которое позволяет получить доступ с IP-адреса клиентской машины для порта 443.

### <a name="check-route-table"></a>Проверка таблицы маршрутов

   Если подсеть кластера имеет установку принудительного туннелирования к брандмауэру (подсеть с [таблицей маршрутов,](/azure/virtual-network/virtual-networks-udr-overview) содержащей маршрут по умолчанию '0.0.0.0/0'), убедитесь, что IP-адрес машины имеет маршрут со [следующим типом перехода](/azure/virtual-network/virtual-networks-udr-overview) в VirtualNetwork/Internet. Этот маршрут необходим для предотвращения асимметричных проблем с маршрутом.

## <a name="ingestion-issues"></a>Проблемы с заглатом

Если у вас возникли проблемы с пригласением, и вы подозреваете, что это связано с виртуальной настройкой сети, выполните следующие шаги.

### <a name="check-ingestion-health"></a>Проверка работоспособности при веке

Убедитесь, что [метрики приема кластера](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) указывают на работоспособное состояние.

### <a name="check-security-rules-on-data-source-resources"></a>Проверка правил безопасности на ресурсах источника данных

Если метрики указывают на то, что из источника данных не было обработано событий *(события, обработанные* метрикой для концентраторов событий/IoT), убедитесь, что ресурсы источника данных (Event Hub или Storage) позволяют получить доступ из подсети кластера в правилах брандмауэра или конечных точках службы.

### <a name="check-security-rules-configured-on-clusters-subnet"></a>Проверка правил безопасности, настроенных на подсеть кластера

Убедитесь, что в подсети кластера есть правила NSG, UDR и брандмауэра должным образом настроены. Кроме того, тестируемое подключение к сети для всех зависимых конечных точек. 

## <a name="cluster-creation-and-operations-issues"></a>Вопросы создания кластеров и операций

Если у вас возникли проблемы с созданием кластеров или эксплуатацией, и вы подозреваете, что это связано с виртуальной настройкой сети, выполните следующие действия, чтобы устранить неполадки.

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>Диагностика виртуальной сети с помощью REST API

[ARMClient](https://chocolatey.org/packages/ARMClient) используется для вызова REST API с помощью PowerShell. 

1. выполните вход с помощью ARMClient;

   ```powerShell
   armclient login
   ```

1. Вызывать операцию диагностики

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. Проверка ответа

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. Ожидание завершения операции

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   Подождите, пока свойство *статуса* не покажет *Завершено,* затем поле *свойств* должно показать:

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

Если свойство *Выводы* показывает пустой результат, это означает, что все сетевые тесты пройдены и никакие соединения не сломаны. Если отображается следующая ошибка, *исходящие зависимости ''dependencyName': «Port» могут быть не удовлетворены (Outbound),* кластер не может достичь зависимых конечных точек службы. Продолжайте следующие шаги.

### <a name="check-network-security-group-nsg"></a>Проверить группу сетевой безопасности (NSG)

Убедитесь, что [группа сетевой безопасности](/azure/virtual-network/security-overview) настроена должным образом в соответствии с инструкциями в [зависимостях для развертывания VNet](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)

### <a name="check-route-table"></a>Проверка таблицы маршрутов

Если подсеть кластера имеет форс-туннелирование, настроенное на брандмауэр (субнет с [маршрутной таблицей,](/azure/virtual-network/virtual-networks-udr-overview) содержащей маршрут по умолчанию '0.0.0.0/0') убедитесь, что [IP-адреса управления](vnet-deployment.md#azure-data-explorer-management-ip-addresses)и [IP-адреса мониторинга работоспособности](vnet-deployment.md#health-monitoring-addresses) имеют маршрут со [следующим типом перехода](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *Internet,* и [приставка исходного адреса](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) к *'management-ip/32'* и *'health-monitoring-ip/32'.* Этот маршрут требуется для предотвращения асимметричных проблем с маршрутом.

### <a name="check-firewall-rules"></a>Проверка правил брандмауэра

Если вы заставите туннельподизавого исходящего трафика к брандмауэру, убедитесь, что все зависимости ФЗДН (например, *.blob.core.windows.net)* разрешены в конфигурации брандмауэра, как описано при [обеспечении исходящего трафика брандмауэром.](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall)
