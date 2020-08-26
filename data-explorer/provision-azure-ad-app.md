---
title: Создание приложения Azure AD в Azure обозреватель данных
description: Узнайте, как создать приложение Azure AD в обозреватель данных Azure.
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/01/2020
ms.openlocfilehash: 5cffee705c6a9225112e7ada8154084de40035c4
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875027"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>Создание Azure Active Directory регистрации приложения в Azure обозреватель данных

Проверка подлинности приложения Azure Active Directory (Azure AD) используется для приложений, таких как автоматическая служба или запланированный поток, которым требуется доступ к обозреватель данных Azure без предоставления пользователю. Если вы подключаетесь к базе данных Azure обозреватель данных с помощью приложения, например веб-приложения, необходимо пройти проверку подлинности с помощью субъекта службы. В этой статье подробно описано, как создать и зарегистрировать субъект-службу Azure AD, а затем авторизовать его для доступа к базе данных Azure обозреватель данных.

## <a name="create-azure-ad-application-registration"></a>Создание регистрации приложения Azure AD

Для аутентификации приложения Azure AD требуется создание и регистрация приложения в Azure AD. При создании регистрации приложения в клиенте Azure AD автоматически создается субъект-служба. 

1. Войдите в [портал Azure](https://portal.azure.com) и откройте `Azure Active Directory` колонку

    ![Выберите Azure Active Directory в меню портала.](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. Выберите колонку **Регистрация приложений** и нажмите кнопку **создать регистрацию** .

    ![Запуск регистрации нового приложения](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. Заполните следующие сведения: 

    * **имя**; 
    * **Поддерживаемые типы учетных записей**
    * **URI**  >  перенаправления **Веб-сайт**
        > [!IMPORTANT] 
        > Тип приложения должен быть **Web**. URI является необязательным и в данном случае остается пустым.
    * Нажмите кнопку **Зарегистрировать**.

    ![Регистрация регистрации нового приложения](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. Выберите колонку **Обзор** и скопируйте **идентификатор приложения**.

    > [!NOTE]
    > Идентификатор приложения необходим для авторизации субъекта-службы для доступа к базе данных.

    ![Копировать идентификатор регистрации приложения](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. В колонке **& секреты** выберите **новый секрет клиента** .

    ![Начать создание секрета клиента](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > В этой статье описывается использование секрета клиента для учетных данных приложения.  Сертификат X509 можно также использовать для проверки подлинности приложения. Выберите **отправить сертификат** и следуйте инструкциям по отправке открытой части сертификата.

1. Введите описание, срок действия и нажмите кнопку **Добавить** .

    ![Введите параметры секрета клиента](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. Скопируйте значение ключа.

    > [!NOTE]
    > При выходе из этой страницы значение ключа будет недоступно.  Вам потребуется ключ для настройки учетных данных клиента для базы данных.

    ![Копировать значение секретного ключа клиента](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

Приложение будет создано. Если вам нужен только доступ к полномочному ресурсу Azure обозреватель данных, как показано в программном примере ниже, пропустите следующий раздел. Сведения о поддержке делегированных разрешений см. [в статье Настройка делегированных разрешений для регистрации приложения](#configure-delegated-permissions-for-the-application-registration).

## <a name="configure-delegated-permissions-for-the-application-registration"></a>Настройка делегированных разрешений для регистрации приложения

Если приложению требуется доступ к обозреватель данных Azure с помощью учетных данных вызывающего пользователя, настройте делегированные разрешения для регистрации приложения. Например, если вы создаете веб-API для доступа к Azure обозреватель данных и хотите пройти проверку подлинности с помощью учетных данных пользователя, *вызывающего* API.  

1. В колонке **разрешения API** выберите **Добавить разрешение**.
1. Выберите **API, используемые моей организацией**. Найдите и выберите **Azure обозреватель данных**.

    ![Добавление разрешения API Azure обозреватель данных](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. В окне **делегированные разрешения**выберите **user_impersonation** и **добавьте разрешения** .

    ![Выбор делегированных разрешений с олицетворением пользователя](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>Предоставление субъекту-службе доступа к базе данных обозреватель данных Azure

Теперь, когда регистрация приложения субъекта-службы будет создана, необходимо предоставить соответствующему субъекту-службе доступ к базе данных Azure обозреватель данных. 

1. В [пользовательском веб-интерфейсе](https://dataexplorer.azure.com/)подключитесь к базе данных и откройте вкладку запрос.

1. Выполните следующую команду:

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    Пример.
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    Последний параметр — это строка, которая отображается в виде примечаний при запросе ролей, связанных с базой данных.
    
    > [!NOTE]
    > После создания регистрации приложения может пройти несколько минут, пока Azure обозреватель данных не сможет ссылаться на него. Если при выполнении команды появится сообщение о том, что приложение не найдено, подождите и повторите попытку.

Дополнительные сведения см. в разделе [разрешения](kusto/api/netfx/kusto-ingest-client-permissions.md)на [Управление ролями безопасности](kusto/management/security-roles.md) и прием.  

## <a name="using-application-credentials-to-access-a-database"></a>Использование учетных данных приложения для доступа к базе данных

Используйте учетные данные приложения для программного доступа к базе данных с помощью [клиентской библиотеки Azure обозреватель данных](kusto/api/netfx/about-kusto-data.md).

```C#
. . .
string applicationClientId = "<myClientID>";
string applicationKey = "<myApplicationKey>";
. . .
var kcsb = new KustoConnectionStringBuilder($"https://{clusterName}.kusto.windows.net/{databaseName}")
    .WithAadApplicationKeyAuthentication(
        applicationClientId,
        applicationKey,
        authority);
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery($"{query}");
```

   > [!NOTE]
   > Укажите идентификатор и ключ приложения, созданные ранее для регистрации приложения (субъект-служба).

Дополнительные сведения см. в статье [Аутентификация в Azure AD для azure обозреватель данных доступ](kusto/management/access-control/how-to-authenticate-with-aad.md) и [Использование Azure Key Vault с веб-приложением .NET Core](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="invalid-resource-error"></a>Ошибка недопустимого ресурса

Если приложение используется для проверки подлинности пользователей или приложений для Azure обозреватель данных Access, необходимо настроить делегированные разрешения для приложения службы обозреватель данных Azure. Объявление приложения может проверять подлинность пользователей или приложений для Azure обозреватель данных Access. В противном случае при попытке проверки подлинности будет возникать ошибка, аналогичная следующей:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

Необходимо выполнить инструкции по [настройке делегированных разрешений для приложения службы Обозреватель данных Azure](#configure-delegated-permissions-for-the-application-registration).

### <a name="enable-user-consent-error"></a>Включить ошибку согласия пользователя

Администратор клиента Azure AD может применить политику, которая запрещает пользователям клиента предоставлять согласие на доступ к приложениям. Эта ситуация приведет к ошибке, похожей на следующую, когда пользователь пытается войти в приложение:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

Необходимо обратиться к администратору Azure AD, чтобы предоставить согласие для всех пользователей в клиенте, или разрешить согласие пользователя для конкретного приложения.

## <a name="next-steps"></a>Дальнейшие шаги

* Список поддерживаемых строк подключения см. в разделе [строки подключения Kusto](kusto/api/connection-strings/kusto.md) .
