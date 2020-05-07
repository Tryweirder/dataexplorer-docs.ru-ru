---
title: Создание кластера Azure обозреватель данных & DB в виртуальной сети
description: Из этой статьи вы узнаете, как создать кластер Azure обозреватель данных в виртуальной сети.
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 80936da3e56fe0a73dcf146ba61630ceeb4bcb2a
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618519"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>Создание кластера Azure обозреватель данных в виртуальной сети

Azure обозреватель данных поддерживает развертывание кластера в подсети в виртуальной сети (VNet). Эта возможность позволяет получить доступ к кластеру в частном порядке из виртуальной сети Azure или локального доступа к ресурсам, таким как концентраторы событий и хранилища в виртуальной сети, а также ограничивать входящий и исходящий трафик.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* Войдите на [портал Azure](https://portal.azure.com/).

## <a name="create-network-security-group-nsg"></a>Создание группы безопасности сети (NSG)

[Группы безопасности сети (NSG)](/azure/virtual-network/security-overview) обеспечивают возможность управления доступом к сети в виртуальной сети. Доступ к обозреватель данных Azure можно получить с помощью двух конечных точек HTTPs (443) и TDS (1433). Следующие правила NSG должны быть настроены таким образом, чтобы разрешить доступ к этим конечным точкам для управления, мониторинга и правильной работы кластера.

Чтобы создать группу безопасности сети, выполните следующие действия.

1. Нажмите кнопку **+ создать ресурс** в левом верхнем углу портала.
1. Поиск *группы безопасности сети*.
1. В разделе **Группа безопасности сети**в нижней части экрана щелкните **создать**.
1. В окне **Создание группы безопасности сети** введите следующие сведения.

   ![Создание формы NSG](media/vnet-create-cluster-portal/network-security-group.png)

    **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | Подписка | Ваша подписка | Выберите подписку Azure, которую вы хотите использовать для кластера.|
    | Группа ресурсов | Ваша группа ресурсов | Воспользуйтесь существующей группой ресурсов или создайте новую. |
    | Имя | азуредатаексплорернсг | Выберите имя, идентифицирующее группу безопасности сети (NSG) в группе ресурсов.  |
    | Регион | *западная часть США* | Выберите регион в соответствии со своими потребностями.
    | | | |

1. Выберите **Отзыв и создание**, чтобы просмотреть свои сведения о кластере, и нажмите кнопку **Создать**, чтобы подготовить кластер.

1. Когда развертывание будет завершено, выберите **Перейти ресурсу**.

    ![Переход к ресурсу](media/vnet-create-cluster-portal/notification-nsg.png)

1. На вкладке **правила безопасности для входящего трафика** выберите **+ Добавить**.
1. В окне **Добавление правила безопасности для входящего трафика** введите следующие сведения.

    ![Форма создания правила для входящего подключения NSG](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **Параметр** | **Рекомендуемое значение** 
    |---|---|
    | Источник | сервицетаг
    | Тег службы источника | азуредатаексплорерманажемент
    | Диапазоны исходных портов | *
    | Назначение | Виртуальная сеть
    | Диапазоны портов назначения | *
    | Протокол | TCP
    | Действие | Allow
    | Приоритет | 100
    | Имя | алловазуредатаексплорерманажемент
    | | |
    
1. Повторите предыдущие два шага для всех входящих и исходящих зависимостей в соответствии с [зависимостями для развертывания виртуальной сети](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment). Кроме того, правила исходящего трафика можно заменить одним правилом, чтобы разрешить доступ *через Интернет* к портам 443 и 80.
    
    Правила NSG для входящих и исходящих зависимостей должны выглядеть следующим образом:

    ![Правила групп безопасности сети (NSG)](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>Создание общедоступных IP-адресов

Чтобы создать общедоступные IP-адреса запросов (Engine), сделайте следующее:

1. Нажмите кнопку **+ создать ресурс** в левом верхнем углу портала.
1. Выполните поиск по запросу *общедоступного IP-адреса*.
1. В колонке **Общедоступный IP-адрес** щелкните **Создать**.
1. В области **Создание общедоступного IP-адреса** заполните следующие сведения.
   
   ![Создание формы общедоступного IP-адреса](media/vnet-create-cluster-portal/public-ip-blade.png)

    **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | Версия IP-адреса | IPv4 | Выберите версию IP-адреса. Поддерживается только IPv4.|
    | Sku | Standard | Необходимо указать конечную точку URI **стандарта** для запроса (Engine). |
    | Имя | обработчик — PIP | Выберите имя, идентифицирующее общедоступный IP-адрес в группе ресурсов.
    | Подписка | Ваша подписка | Выберите подписку Azure, которую вы хотите использовать для общедоступного IP-адреса.|
    | Группа ресурсов | Ваша группа ресурсов | Воспользуйтесь существующей группой ресурсов или создайте новую. |
    | Расположение | *западная часть США* | Выберите регион в соответствии со своими потребностями.
    | | | |

1. Выберите **создать** , чтобы создать общедоступный IP-адрес.

1. Чтобы создать общедоступный IP-адрес приема (Управление данными), следуйте тем же инструкциям и выберите 
    * **SKU**: базовый
    * **Назначение IP-адресов**: статическое

## <a name="create-virtual-network-and-subnet"></a>Создание виртуальной сети и подсети

Чтобы создать виртуальную сеть и подсеть, выполните следующие действия.

1. Нажмите кнопку **+ создать ресурс** в левом верхнем углу портала.
1. Выполните поиск по запросу *Виртуальная сеть*.
1. В разделе **Виртуальная сеть**в нижней части экрана щелкните **создать**.
1. В окне **Создание виртуальной сети** заполните следующие сведения.

   ![Форма создания виртуальной сети](media/vnet-create-cluster-portal/vnet-blade.png)

    **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | Подписка | Ваша подписка | Выберите подписку Azure, которую вы хотите использовать для кластера.|
    | Группа ресурсов | Ваша группа ресурсов | Воспользуйтесь существующей группой ресурсов или создайте новую. |
    | Имя | азуредатаексплорервнет | Выберите имя, идентифицирующее виртуальную сеть в группе ресурсов.
    | Регион | *западная часть США* | Выберите регион в соответствии со своими потребностями.
    | | | |

    > [!NOTE]
    > Для рабочих нагрузок спланируйте размер подсети в зависимости от [размера подсети плана в виртуальной сети](/azure/data-explorer/vnet-deployment#plan-subnet-size-in-your-vnet) .

1. Выберите **Отзыв и создание**, чтобы просмотреть свои сведения о кластере, и нажмите кнопку **Создать**, чтобы подготовить кластер.

1. Когда развертывание будет завершено, выберите **Перейти ресурсу**.
1. Перейдите в колонку **подсети** и выберите подсеть **по умолчанию** .
    
    ![Колонка "подсети"](media/vnet-create-cluster-portal/subnets.png)

1. В окне подсети **по умолчанию** :
    1. Выберите **группу безопасности сети** в раскрывающемся меню. 
    1. Выберите имя группы безопасности сети, в данном случае **азуредатаексплорернсг**. 
    1. **Сохранить**

    ![Настройка подсети](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>Создание кластера

Создайте кластер Azure обозреватель данных с определенным набором ресурсов вычислений и хранения в группе ресурсов Azure, как описано в разделе [Создание кластера](create-cluster-database-portal.md#create-a-cluster).

1. Перед завершением создания кластера в окне **Создание кластера Azure обозреватель данных** перейдите на вкладку **сеть** , чтобы указать сведения о виртуальной сети, используя ресурсы, созданные на предыдущих вкладках.

   ![Форма создания виртуальной сети кластера](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | Подписка | Ваша подписка | Выберите подписку Azure, которую вы хотите использовать для сетевых ресурсов.|
    | Виртуальная сеть | азуредатаексплорервнет | Выберите виртуальную сеть, созданную на предыдущих шагах.
    | Подсеть | default | Выберите подсеть, созданную на предыдущих шагах.
    | Запрос общедоступного IP-адреса | обработчик — PIP | Выберите общедоступный IP-адрес запроса, созданный на предыдущих шагах.
    | Общедоступный IP-адрес приема данных | DM — PIP | Выберите общедоступный IP-адрес приема, созданный на предыдущих шагах.
    | | | |

1. Выберите **проверить и создать** , чтобы создать кластер.
1. Когда развертывание будет завершено, выберите **Перейти ресурсу**.

Чтобы развернуть кластер Azure обозреватель данных в виртуальной сети, используйте [развертывание кластера azure обозреватель данных в](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) шаблоне виртуальной сети Azure Resource Manager.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Развертывание обозреватель данных Azure в виртуальной сети](vnet-deployment.md)