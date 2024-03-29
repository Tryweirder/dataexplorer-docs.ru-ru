---
title: Предоплата за разметку обозреватель данных Azure для экономии денег
description: Узнайте, как купить зарезервированную емкость Azure обозреватель данных, чтобы сэкономить на стоимости Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: aefbde03b123e9c5413b1ed39e85db096f3ff3d7
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343459"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Предоплата за обозреватель данных Azure для единиц разметки с зарезервированной емкостью Azure обозреватель данных

Экономия денег с помощью обозреватель данных Azure за счет предоплаты за единицы разметки по сравнению с ценами оплаты по мере использования. Используя зарезервированную емкость Azure обозреватель данных, вы предследите за использование Azure обозреватель данных использовать в течение одного или трех лет, чтобы получить существенную скидку по затратам на разметку обозреватель данных Azure. Чтобы приобрести Azure обозреватель данных зарезервированную емкость, необходимо указать только этот термин, он будет применяться ко всем развертываниям обозреватель данных Azure во всех регионах.

Приобретая резервирование, вы заранее оплачиваете расходы на разметку в течение одного или трех лет. После приобретения резервирования плата за разметку обозреватель данных Azure, которая соответствует атрибутам резервирования, больше не взимается по тарифам с оплатой по мере использования. Кластеры обозреватель данных Azure, которые уже запущены или развертывались недавно, автоматически получат преимущество. Это резервирование не охватывает затраты на вычисление, сеть и хранение, связанные с кластерами. Зарезервированная емкость для этих ресурсов должна быть приобретена отдельно. По окончании срока резервирования срок действия выставления счетов истекает, а для единиц разметки обозреватель данных Azure выставляются счета по цене оплаты по мере использования. Резервирования не обновляются автозаменой. Сведения о ценах см. на [странице цен на обозреватель данных Azure](https://azure.microsoft.com/pricing/details/data-explorer/).

Вы можете приобрести зарезервированную емкость Azure обозреватель данных в [портал Azure](https://portal.azure.com). Чтобы купить зарезервированную емкость Azure обозреватель данных:

* Необходимо быть владельцем по крайней мере одной подписки Enterprise или с оплатой по мере использования.
* Для подписок с соглашением Enterprise параметр **Добавить зарезервированные экземпляры** следует включить на [портале EA](https://ea.azure.com). Кроме того, если этот параметр отключен, необходимо быть администратором EA в подписке.
* Для программы поставщика облачных решений (CSP) только агенты администратора или агенты продаж могут приобрести зарезервированную емкость Azure обозреватель данных.

Дополнительные сведения о том, как клиентские клиенты и клиенты с оплатой по мере использования оплачиваются по покупкам резервирования, см. в следующих статьях:
* [Общие сведения об использовании зарезервированных экземпляров Azure с Соглашением о регистрации Enterprise](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea) 
* [Сведения об использовании резервирования Azure для подписки с оплатой по мере использования](/azure/cost-management-billing/reservations/understand-reserved-instance-usage).

## <a name="determine-the-right-markup-usage-before-purchase"></a>Определение правильного использования разметки перед покупкой

Размер резервирования должен основываться на общем количестве единиц разметки Azure обозреватель данных, используемых существующими или вскоре развертываемыми кластерами Azure обозреватель данных. Число единиц разметки равно числу ядер кластера Azure обозреватель данных Engine в рабочей среде (не включая SKU для разработки и тестирования). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Приобретение зарезервированной емкости Azure обозреватель данных

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите **все службы**  >  **резервирования**  >  **покупки**. Выберите **Добавить**.
1. На панели **Выбор типа продукта** выберите **Azure обозреватель данных** , чтобы приобрести новое резервирование для единиц разметки обозреватель данных Azure. 
1. Выберите **купить**
1. Заполните обязательные поля. 

    ![Страница «Покупка»](media/pricing-reserved-capacity/purchase-page.png)

1. Изучите стоимость резервирования зарезервированной емкости в Azure обозреватель данных в разделе **затраты** .
1. Щелкните **Приобрести**.
1. Выберите **Просмотреть резервирование**, чтобы просмотреть состояния покупки.

## <a name="cancellations-and-exchanges"></a>Отмена и обмен

Если вам нужно отменить зарезервированную резервную емкость Azure обозреватель данных, может взиматься плата за более 12%. Возмещение основано на наименьшей цене цены покупки или текущей стоимости резервирования. Максимальная сумма возмещенных средств — 50 000 долл. США в год. Полученные возмещенные средства включают оставшийся пропорциональный баланс за вычетом 12 % за досрочное аннулирование. Если нужно запросить отмену, перейдите к резервированию на портале Azure и выберите **Возместить**, чтобы создать запрос в службу поддержки.

Если вам нужно изменить резервирование резервирования емкости Azure обозреватель данных на другой термин, вы можете обмениваться данными с другой зарезервированной единицей, которая равна или больше. При обмене дата начала использования для нового резервирования не переносится из текущего резервирования. Срок использования (1 или 3 года) начинается с момента создания резервирования. Если нужно запросить обмен, перейдите в резервирование на портале Azure и выберите **Обмен**, чтобы создать запрос в службу поддержки.

Дополнительные сведения об обмене и возмещении резервирования см. в статье [Обмен резервированиями и](/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)возврат денег.

## <a name="manage-your-reserved-capacity-reservation"></a>Управление резервированиями зарезервированных мощностей

Скидка на резервирование единиц разметки Azure обозреватель данных автоматически применяется к числу единиц разметки, соответствующих зарезервированным областям резервирования емкости и атрибутам Azure обозреватель данных. 


> [!NOTE]
> * Вы можете обновить область резервирования зарезервированных ресурсов Azure обозреватель данных с помощью [портал Azure](https://portal.azure.com), POWERSHELL, CLI или API.
> * Чтобы узнать, как управлять зарезервированной емкостью обозреватель данных Azure, см. статью Управление зарезервированной [емкостью azure обозреватель данных](/azure/cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о резервировании в Azure см. по следующим ссылкам:

* [Основные сведения о резервировании в Azure](/azure/cost-management-billing/reservations/save-compute-costs-reservations)
* [Управление Azure Reserved VM Instances](/azure/cost-management-billing/reservations/manage-reserved-vm-instance)
* [Сведения о скидках на резервирование Azure](/azure/cost-management-billing/reservations/understand-reservation-charges)
* [Общие сведения об использовании резервирования Azure для подписки с оплатой по мере использования](/azure/cost-management-billing/reservations/understand-reserved-instance-usage)
* [Общие сведения об использовании зарезервированных экземпляров Azure с Соглашением о регистрации Enterprise](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea)
* [Приобретение зарезервированных экземпляров Azure](/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).