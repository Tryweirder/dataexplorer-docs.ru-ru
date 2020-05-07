---
title: Практическое руководство. Создание приложения AAD с помощью Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается, как создать приложение AAD в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 0816d55cda6d29820084514b0bfec27d726693f9
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617975"
---
# <a name="howto-creating-an-aad-application"></a>Практическое руководство. Создание приложения AAD

Хотя проверка подлинности пользователя AAD очень проста (поскольку пользователи определяются в AAD администратором клиента, например MSIT), проверка подлинности приложения AAD несколько сложнее. Это связано с тем, что для этого требуется создать и зарегистрировать приложение в AAD. Этот процесс описан ниже в некоторых сведениях.

Проверка подлинности приложений AAD полезна для приложений, которым необходим доступ к Kusto без входа пользователя в систему или его присутствия (например, автоматическая служба или Запланированная последовательность).

Эти модели следует избегать в клиентских приложениях и приложениях среднего уровня, имеющих интерактивный контекст пользователя. Это обусловлено тем, что авторизация выполняется на основе удостоверения приложения AAD, а не удостоверения пользователя, поэтому вызывающему приложению потребуется реализовать собственную логику авторизации, чтобы предотвратить неправильное использование.

## <a name="application-authentication-use-cases"></a>Варианты использования проверки подлинности приложения

Существует два основных сценария использования проверки подлинности приложения.
* Приложения, которые напрямую получают доступ к службе Kusto и от их имени;
* Приложения, которые будут проходить проверку подлинности пользователей в Kusto (Делегированная аутентификация)

## <a name="1-provisioning-a-new-application"></a>1. Подготовка нового приложения

#### <a name="register-the-new-application"></a>Регистрация нового приложения

1. Войдите в [портал Azure](https://portal.azure.com) и откройте `Azure Active Directory` колонку.

    :::image type="content" source="images/aad-create-app-step-0.png" alt-text="Действие создания приложения AAD, шаг 0":::

1. Выберите `App registrations` и при загрузке колонки выберите `New application registration`.

    :::image type="content" source="images/aad-create-app-step-1.png" alt-text="Создание приложения AAD, шаг 1":::

1. Заполните сведения о приложении:
    * Имя
    * Тип: задайте значение.`Web app/API`
    * URL-адрес входа: URL-адрес, используемый пользователями для доступа к приложению. AAD не проверяет URL-адрес,<br>
        но требует указать значение. Поэтому, если у приложения нет URL-адреса, доступного пользователям,<br>
        Введите URL-адрес, принадлежащий приложению (например, https://<APP-CNAME> или https://<CLOUD-SERVICE-NAME>. cloudapp.net).<br>
        Можно даже указать значение и продолжить, если приложение еще не было написано.

    
    :::image type="content" source="images/aad-create-app-step-2.png" alt-text="Действие 2. Создание приложения AAD":::

1. Когда приложение будет готово, откройте его `Settings` колонку.

    :::image type="content" source="images/aad-create-app-step-3.png" alt-text="Создание AAD, шаг 3":::

1. В `Keys` колонке настройте проверку подлинности для нового приложения:
    * Если вы хотите использовать общий ключ, выберите длительность ключа из раскрывающегося меню и скопируйте ключ при его создании.
        Вы не сможете восстановить этот ключ.
    * Кроме того, для проверки подлинности приложения можно использовать сертификат X509.
        Для этого выберите `Upload Public Key` и следуйте инструкциям по отправке открытой части сертификата.
    * Не забудьте выбрать `Save` , когда закончите.

    :::image type="content" source="images/aad-create-app-step-4.png" alt-text="Создание приложения AAD, шаг 4":::

Ваше приложение настроено. Если вам нужно только получить доступ к Kusto с созданным приложением, то все готово.

#### <a name="set-up-delegated-permissions-for-kusto-service-application"></a>Настройка делегированных разрешений для приложения службы Kusto

Если требуется, чтобы приложение могло выполнять проверку подлинности пользователя или приложения для Kusto доступа, необходимо настроить доверие между приложением и Kusto:

1. В `Required permissions` колонке выберите `Add`.

    :::image type="content" source="images/aad-create-app-step-5.png" alt-text="Создание приложения AAD, шаг 5":::
   
1. Выберите `Select an API`, введите `KustoService` в поле фильтра и выберите `KustoService (Kusto)`.
1. Если для кластеров Kusto требуется MFA, `KustoMFA` введите в поле фильтра и выберите `KustoServiceMFA (KustoMFA)`.

    :::image type="content" source="images/aad-create-app-step-6.png" alt-text="Создание приложения AAD, шаг 6":::

1. После подтверждения выбора выберите делегированные разрешения для `Access Kusto`.

   :::image type="content" source="images/aad-create-app-step-7.png" alt-text="Создание приложения AAD, шаг 7":::

1. Выберите `Done` , чтобы завершить процесс.


### <a name="2-set-permissions-to-the-application-on-kusto-cluster"></a>2. Задание разрешений для приложения в кластере Kusto

> Прежде чем использовать только что подготовленное приложение, убедитесь, что оно разрешается из API Graph.<br>
    Распространение изменений AAD может занять до нескольких часов.

1. Получите доступ к администратору базы данных, чтобы предоставить разрешения новому приложению.
Дополнительные сведения о настройке разрешений см. в разделе [Управление участниками базы данных](../security-roles.md) .<br>
Сведения о настройке разрешений на прием см. в разделе [разрешения приема](../../api/netfx/kusto-ingest-client-permissions.md).
1. Добавьте подключение к этой службе в обозревателе Kusto, если она еще не установлена.

### <a name="3-application-can-now-access-kusto"></a>3. Теперь приложение может получать доступ к Kusto.

При использовании нового подготовленного приложения для доступа к кластерам Kusto с помощью клиентских библиотек Kusto укажите следующую строку подключения (если вы решили выполнить аутентификацию с помощью общего ключа):

`https://`*ClusterDnsName*`/;Federated Security=True;Application Client Id=`*ApplicationClientId*аппликатионклиентид`;Application Key=`*ApplicationKey*


### <a name="appendix-a-aad-errors"></a>Приложение а. ошибки AAD

#### <a name="aad-error-aadsts650057"></a>Ошибка AAD AADSTS650057

Если приложение используется для проверки подлинности пользователей или приложений для Kusto доступа, необходимо настроить делегированные разрешения для приложения службы Kusto, т. е. Объявите, что приложение может проверять подлинность пользователей или приложений для Kusto доступа.
В противном случае при попытке проверки подлинности будет возникать ошибка, аналогичная следующей:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

Необходимо выполнить инструкции по [настройке делегированных разрешений для приложения службы Kusto](#set-up-delegated-permissions-for-kusto-service-application).

#### <a name="enable-user-consent-if-needed"></a>При необходимости включить согласие пользователя

Администратор клиента AAD может применить политику, которая запрещает пользователям клиента предоставлять согласие на доступ к приложениям. Это приведет к ошибке, подобной приведенной ниже, когда пользователь пытается войти в приложение:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

Необходимо попросить администратора AAD предоставить согласие для всех пользователей в клиенте или разрешить согласие пользователя для конкретного приложения.

### <a name="appendix-b-advanced-topics-and-troubleshooting"></a>Приложение б. Дополнительные разделы и устранение неполадок

* Полный справочник по поддерживаемым строкам подключения см. в документации по [строкам подключения Kusto](../../api/connection-strings/kusto.md) .