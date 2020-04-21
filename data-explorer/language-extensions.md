---
title: Управление расширением языка в кластере Azure Data Explorer.
description: Используйте расширение языка для интеграции других языков в запросы Azure Data Explorer K'L.
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 52855651daccfe3c9baf5bb059530bcf7bfb0f19
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81502534"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>Управление расширением языка в кластере Azure Data Explorer (Предварительный просмотр)

Функция расширения языка позволяет использовать плагины расширения языка для интеграции других языков в запросы Azure Data Explorer. При запуске пользователь-определенной функции (UDF) с помощью соответствующего скрипта, скрипт получает табелярные данные в качестве ввода и, как ожидается, будет производить таблетчатый выход. Время выполнения плагина размещается в [песочнице,](kusto/concepts/sandboxes.md)изолированной и безопасной среде, работая на узлах кластера. В этой статье вы управляете плагином языковых расширений в кластере Azure Data Explorer на портале Azure.

> [!NOTE]
> Языковыми расширениями Azure Data Explorer, которые в настоящее время поддерживаются, являются Python и R.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* Создайте [кластер и базу данных Azure Data Explorer](create-cluster-database-portal.md).

## <a name="enable-language-extensions-on-your-cluster"></a>Включить расширение языка в кластере

> [!WARNING]
> Пожалуйста, просмотрите [ограничения,](#limitations) прежде чем включить расширение языка.

Сделайте следующие шаги, чтобы включить расширение языка в кластере:

1. На портале Azure перейдите к кластеру Azure Data Explorer. 
1. В **настройках**выберите **Конфигурации.** 
1. В панели **конфигураций** выберите **On,** чтобы включить расширение языка.
1. Щелкните **Сохранить**.
 
    ![языковое расширение на](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> Включение процесса расширения языка может занять несколько минут. В течение этого времени ваш кластер будет приостановлен.
 
## <a name="run-language-extension-integrated-queries"></a>Запуск интегрированных запросов на расширение языка

* Узнайте, как [запустить интегрированные запросы Python на K'L.](kusto/query/pythonplugin.md)
* Узнайте, как [запускать R интегрированные запросы к КЗЛ.](kusto/query/rplugin.md) 

## <a name="disable-language-extensions-on-your-cluster"></a>Отключить расширение языка в кластере

> [!NOTE]
> Отключение языковых расширений может занять несколько минут.

Сделайте следующие шаги, чтобы отключить расширение языка в кластере:

1. На портале Azure перейдите к кластеру Azure Data Explorer. 
1. В **настройках**выберите **Конфигурации.** 
1. В панели **конфигураций** выберите **Off,** чтобы отключить расширение языка.
1. Щелкните **Сохранить**.

    ![Расширение языка выключено](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>Ограничения

* Функция языковых расширений не поддерживает [шифрование диска.](manage-cluster-security.md) 
* Языковые расширения в песочнице времени выполнения распределяют дисковое пространство, даже если запрос не выполняется в рамках соответствующего языка.
См [песочницы](kusto/concepts/sandboxes.md) для более подробных ограничений.
