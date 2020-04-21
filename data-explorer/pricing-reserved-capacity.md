---
title: Предоплата за разметку Azure Data Explorer, чтобы сэкономить деньги
description: Узнайте, как купить зарезервированную емкость Azure Data Explorer, чтобы сэкономить на затратах Azure Data Explorer.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 6475a0190546ac577ae86ac1fde4adc41cd47595
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501741"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Предоплата за блоки разметки Azure Data Explorer с зарезервированной емкостью Azure Data Explorer

Экономьте деньги с помощью Azure Data Explorer, предоплату за единицы разметки по сравнению с ценами с оплатой по мере использования. С зарезервированной емкостью Azure Data Explorer вы берете на себя обязательство использовать Azure Data Explorer в течение одного или трех лет, чтобы получить значительную скидку на затраты azure Data Explorer на разметку. Чтобы приобрести зарезервированную емкость Azure Data Explorer, нужно только указать термин, он будет применяться ко всем развертываниям Azure Data Explorer во всех регионах.

Приобретая бронирование, вы предварительно оплачиваете расходы на разметку в течение одного или трех лет. Как только вы покупаете бронирование, наценка Azure Data Explorer, которая соответствует атрибутам бронирования, больше не взимается по тарифам с оплатой по мере использования. Кластеры Azure Data Explorer, которые уже запущены или недавно развернуты, автоматически получат выгоду. Это резервирование не охватывает вычисления, сетевые системы или сборы за хранение, связанные с кластерами. Зарезервированные мощности для этих ресурсов необходимо приобрести отдельно. В конце срока бронирования срок действия платежного пособия истекает, а единицы разметки Azure Data Explorer выставляются по цене оплаты по мере использования. Бронирование не обновляется автоматически. Для получения информации о ценах смотрите [страницу ценообразования Azure Data Explorer.](https://azure.microsoft.com/pricing/details/data-explorer/)

Зарезервированную емкость Azure Data Explorer можно на [портале Azure.](https://portal.azure.com) Чтобы купить зарезервированную емкость Azure Data Explorer:

* Вы должны быть владельцем хотя бы одной подписки Enterprise или Pay-As-You-Go.
* Для подписок с соглашением Enterprise параметр **Добавить зарезервированные экземпляры** следует включить на [портале EA](https://ea.azure.com). Кроме того, если эта настройка отключена, вы должны быть EA Admin по подписке.
* В программе поставщика облачных решений (CSP) только агенты-админы или агенты по продажам могут приобрести зарезервированную емкость Azure Data Explorer.

Для получения подробной информации о том, как корпоративные клиенты и клиенты Pay-As-You-Go взимаются за покупки бронирования, см.:
* [Общие сведения об использовании зарезервированных экземпляров Azure с Соглашением о регистрации Enterprise](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea) 
* [Понять использование бронирования Azure для подписки Pay-As-You-Go.](/azure/cost-management-billing/reservations/understand-reserved-instance-usage)

## <a name="determine-the-right-markup-usage-before-purchase"></a>Определить правильное использование разметки перед покупкой

Размер резервирования должен основываться на общем количестве единиц разметки Azure Data Explorer, используемых существующими или скоро развернутыми кластерами Azure Data Explorer. Количество единиц разметки равно количеству ядер кластера кластера блоков блоков Azure Data Explorer в производстве (без учета Dev/test SKU). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Купить зарезервированную емкость Azure Data Explorer

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите **Все услуги** > **Reservations** > **Бронирование Покупка сейчас**. Выберите **Добавить**
1. В панели **типа «Выберите»** выберите **Azure Data Explorer,** чтобы приобрести новое резервирование для единиц разметки Azure Data Explorer. 
1. Выберите **Купить**
1. Заполните обязательные поля. 

    ![Страница покупки](media/pricing-reserved-capacity/purchase-page.png)

1. Просмотрите стоимость зарезервированного резервирования емкости Azure Data Explorer в разделе **Затраты.**
1. Щелкните **Приобрести**.
1. Выберите **Просмотреть резервирование**, чтобы просмотреть состояния покупки.

## <a name="cancellations-and-exchanges"></a>Отмена и обмен

Если вам необходимо отменить зарезервированное бронирование емкости Azure Data Explorer, может взиматься плата за досрочное прекращение в размере 12%. Возврат средств основан на самой низкой цене покупки или текущей цене бронирования. Максимальная сумма возмещенных средств — 50 000 долл. США в год. Полученные возмещенные средства включают оставшийся пропорциональный баланс за вычетом 12 % за досрочное аннулирование. Если нужно запросить отмену, перейдите к резервированию на портале Azure и выберите **Возместить**, чтобы создать запрос в службу поддержки.

Если необходимо изменить зарезервированное резервирование емкости Azure Data Explorer на другой термин, вы можете обменять его на другое резервирование, имевещее равное или большее значение. При обмене дата начала использования для нового резервирования не переносится из текущего резервирования. Срок использования (1 или 3 года) начинается с момента создания резервирования. Если нужно запросить обмен, перейдите в резервирование на портале Azure и выберите **Обмен**, чтобы создать запрос в службу поддержки.

Для получения дополнительной информации о том, как обменять или вернуть бронирование, [см.](/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)

## <a name="manage-your-reserved-capacity-reservation"></a>Управление зарезервированной резервированием емкости

Скидка на резервирование единиц разметки Azure Data Explorer автоматически применяется к количеству единиц разметки, которые соответствуют зарезервированной области резервирования и атрибутам резервирования данных Azure Data Explorer. 


> [!NOTE]
> * Вы можете обновить область резервирования зарезервированных емкости Azure Data Explorer через [портал Azure,](https://portal.azure.com)PowerShell, CLI или через API.
> * Чтобы узнать, как управлять зарезервированным резервированием емкости Azure Data Explorer, [см.](/azure/cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о резервировании в Azure см. по следующим ссылкам:

* [Что такое бронирование Azure?](/azure/cost-management-billing/reservations/save-compute-costs-reservations)
* [Управление Azure Reserved VM Instances](/azure/cost-management-billing/reservations/manage-reserved-vm-instance)
* [Сведения о скидках на резервирование Azure](/azure/cost-management-billing/reservations/understand-reservation-charges)
* [Общие сведения об использовании резервирования Azure для подписки с оплатой по мере использования](/azure/cost-management-billing/reservations/understand-reserved-instance-usage)
* [Общие сведения об использовании зарезервированных экземпляров Azure с Соглашением о регистрации Enterprise](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea)
* [Приобретение зарезервированных экземпляров Azure](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
