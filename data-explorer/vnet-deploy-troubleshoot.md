---
title: Устранение неполадок доступа, приема и работы кластера Azure обозреватель данных в виртуальной сети
description: Устранение неполадок подключения, приема, создания кластера и работы кластера Azure обозреватель данных в виртуальной сети
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: b0148267f2245cd00e9bd2891b1a220908c1c16a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351664"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>Устранение неполадок доступа, приема и работы кластера Azure обозреватель данных в виртуальной сети

В этом разделе вы узнаете, как устранять неполадки подключения, эксплуатации и создания кластеров для кластера, развернутого в [виртуальной сети](/azure/virtual-network/virtual-networks-overview).

## <a name="access-issues"></a>Проблемы доступа

Если у вас возникли проблемы при доступе к кластеру с помощью общедоступной (cluster.region.kusto.windows.net) или частной (private-cluster.region.kusto.windows.net) конечной точки и вы подозреваете, что он связан с программой установки виртуальной сети, выполните следующие действия для устранения проблемы.

### <a name="check-tcp-connectivity"></a>Проверка TCP-подключения

Первый шаг включает проверку TCP-подключения с помощью ОС Windows или Linux.

# <a name="windows"></a>[Windows](#tab/windows)

1. Скачайте [TCping](https://www.elifulkerson.com/projects/tcping.php) на компьютер, подключающийся к кластеру.
1. Проверьте связь с местом назначения с исходного компьютера, выполнив следующую команду:

   ```cmd
   C:\> tcping -t yourcluster.kusto.windows.net 443 
   ** Pinging continuously.  Press control-c to stop **
   Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
   ```

# <a name="linux"></a>[Linux](#tab/linux)

1. Установка *неткат* на компьютере, подключенном к кластеру

   ```bash
   $ apt-get install netcat
   ```

1. Проверьте связь с местом назначения с исходного компьютера, выполнив следующую команду:

   ```bash
   $ netcat -z -v yourcluster.kusto.windows.net 443
   Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
   ```
---

Если проверка не прошла успешно, перейдите к следующим шагам. Если проверка прошла успешно, это не связано с проблемой подключения по протоколу TCP. Чтобы устранить неполадки, перейдите в раздел " [проблемы в работе](#cluster-creation-and-operations-issues) ".

### <a name="check-the-network-security-group-nsg"></a>Проверка группы безопасности сети (NSG)

Убедитесь, что [Группа безопасности сети](/azure/virtual-network/security-overview) (NSG), подключенная к подсети кластера, имеет правило входящего трафика, которое разрешает доступ с IP-адреса клиентского компьютера для порта 443.

### <a name="check-route-table"></a>Проверка таблицы маршрутов

Если в подсети кластера настроено принудительное туннелирование брандмауэра (подсеть с [таблицей маршрутов](/azure/virtual-network/virtual-networks-udr-overview) , содержащей маршрут по умолчанию "0.0.0.0/0"), убедитесь, что IP-адрес компьютера имеет маршрут с [типом следующего прыжка](/azure/virtual-network/virtual-networks-udr-overview) VirtualNetwork/Интернет. Этот маршрут необходим для предотвращения проблем с асимметричными маршрутами.

## <a name="ingestion-issues"></a>Проблемы приема

Если возникают проблемы приема и вы подозреваете, что он связан с программой установки виртуальной сети, выполните следующие действия.

### <a name="check-ingestion-health"></a>Проверка работоспособности приема

Убедитесь, что [метрики приема кластера](using-metrics.md#ingestion-health-and-performance-metrics) указывают на работоспособное состояние.

### <a name="check-security-rules-on-data-source-resources"></a>Проверка правил безопасности в ресурсах источника данных

Если метрики указывают на то, что никакие события не обрабатывались из источника данных (метрики*обработки* событий для центров событий или IOT), убедитесь, что ресурсы источника данных (концентратор событий или хранилище) разрешают доступ из подсети кластера в правилах брандмауэра или конечных точках службы.

### <a name="check-security-rules-configured-on-clusters-subnet"></a>Проверка правил безопасности, настроенных в подсети кластера

Убедитесь, что в подсети кластера имеются NSG, UDR и правила брандмауэра правильно настроены. Кроме того, проверьте сетевое подключение для всех зависимых конечных точек. 

## <a name="cluster-creation-and-operations-issues"></a>Проблемы создания и эксплуатации кластера

Если возникают проблемы с созданием кластера или операциями, и вы подозреваете, что она связана с настройкой виртуальной сети, выполните следующие действия для устранения проблемы.

### <a name="check-the-dns-servers-configuration"></a>Проверка конфигурации "DNS-серверы"

Пользовательский DNS-сервер не поддерживается. Используйте параметр по умолчанию в разделе конфигурации **DNS-серверов** виртуальной сети.

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>Диагностика виртуальной сети с помощью REST API

[ARMClient](https://chocolatey.org/packages/ARMClient) используется для вызова REST API с помощью PowerShell. 

1. выполните вход с помощью ARMClient;

   ```powerShell
   armclient login
   ```

1. Вызов операции диагностики

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
    
   Дождитесь *завершения*отображения свойства *Status* , а затем в поле *Свойства* должно отобразиться следующее:

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

Если свойство *Findings* Results показывает пустой результат, это означает, что все прошедшие проверку сети и подключения не будут нарушены. Если отображается следующая ошибка, *Исходящая зависимость "{депенденцинаме}: {Port}" может быть не удовлетворена (исходящая)*, кластер не сможет связаться с конечными точками зависимой службы. Выполните следующие действия.

### <a name="check-network-security-group-nsg"></a>Проверка группы безопасности сети (NSG)

Убедитесь, что [Группа безопасности сети](/azure/virtual-network/security-overview) настроена правильно в соответствии с инструкциями в разделе [зависимости для развертывания виртуальной сети](vnet-deployment.md#dependencies-for-vnet-deployment) .

### <a name="check-route-table"></a>Проверка таблицы маршрутов

Если в подсети кластера настроено принудительное туннелирование для брандмауэра (подсеть с [таблицей маршрутов](/azure/virtual-network/virtual-networks-udr-overview) , содержащей маршрут по умолчанию "0.0.0.0/0"), убедитесь, что [IP-адреса управления](vnet-deployment.md#azure-data-explorer-management-ip-addresses)и [IP-адреса мониторинга работоспособности](vnet-deployment.md#health-monitoring-addresses) имеют маршрут со [следующим прыжком типа](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) " *Интернет*", и [префикс адреса источника](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) *:* *"Management-IP/32"* Этот маршрут необходим для предотвращения проблем с асимметричными маршрутами.

### <a name="check-firewall-rules"></a>Проверка правил брандмауэра

При принудительном исходящем трафике подсети туннеля к брандмауэру убедитесь, что в конфигурации брандмауэра разрешены полные доменные имена зависимостей (например, *. BLOB.Core.Windows.NET*), как описано в статье [Защита исходящего трафика с помощью брандмауэра](vnet-deployment.md#securing-outbound-traffic-with-firewall).
