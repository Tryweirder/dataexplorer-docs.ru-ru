---
title: Создание приложения Azure AD в Azure Data Explorer
description: Узнайте, как создать приложение Azure AD в Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 7bbb8064cd71e61b6fdfb1205e783099ec21c687
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501598"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>Создание регистрации приложений active Directory Azure в Azure Data Explorer

Проверка подлинности приложений Azure Active Directory (Azure AD) используется для приложений, таких как служба без присмотра или запланированный поток, которым необходимо получить доступ к Исследователю данных Azure Data Explorer без присутствия пользователя. Если вы подключаетесь к базе данных Azure Data Explorer с помощью приложения, например веб-приложения, следует проверить подлинность с помощью основной аутентификации службы. В этой статье подробно описано, как создать и зарегистрировать принцип службы Azure AD, а затем разрешить ей доступ к базе данных Azure Data Explorer.

## <a name="create-azure-ad-application-registration"></a>Создание регистрации приложений Azure AD

Аутентификация приложения Azure AD требует создания и регистрации приложения в Azure AD. Принцип службы автоматически создается при создании регистрации приложения в аименном АТАКЖЕ Azure. 

1. Войти на [портал Azure](https://portal.azure.com) `Azure Active Directory` и открыть лезвие

    ![Выберите активный каталог Azure из меню портала](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. Выберите лезвие **регистраций приложений** и выберите **новую регистрацию**

    ![Начало регистрации нового приложения](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. Заполните следующие сведения: 

    * **имя**; 
    * **Поддерживаемые типы учетных записей**
    * **Перенаправить URI** > **Web**
        > [!IMPORTANT] 
        > Тип приложения должен быть **вебом**. URI является необязательным и остается пустым в этом случае.
    * Выберите **Регистр**

    ![Регистрация новой регистрации приложений](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. Выберите лезвие **обзора** и скопируйте **идентификатор приложения.**

    > [!NOTE]
    > Идентификатор приложения для разрешения директора службы на доступ к базе данных.

    ![Копирование идентификатора регистрации приложения](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. В **сертификатах & лезвие секретов** выберите **Новый секрет клиента**

    ![Начало создания клиентской тайны](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > В этой статье описывается использование секрета клиента для учетных данных приложения.  Вы также можете использовать сертификат X509 для проверки подлинности приложения. Выберите **сертификат загрузки** и следуйте инструкциям для загрузки общедоступной части сертификата.

1. Введите описание, истечение срока действия и выберите **Добавить**

    ![Введите параметры секрета клиента](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. Скопируйте значение ключа.

    > [!NOTE]
    > Когда вы покидаете эту страницу, ключевое значение будет недоступно.  Для настройки учетных данных клиента в базу данных потребуется ключ.

    ![Копирование клиента секретное ключевое значение](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

Ваше приложение создано. Если вам нужен только доступ к авторизованному ресурсу Azure Data Explorer, например в программном примере ниже, пропустите следующий раздел. Для поддержки делегированных разрешений [см.](#configure-delegated-permissions-for-the-application-registration)

## <a name="configure-delegated-permissions-for-the-application-registration"></a>Настройка делегированных разрешений на регистрацию заявки

Если приложению необходимо получить доступ к Azure Data Explorer с помощью учетных данных пользователя вызова, назначьте делегированные разрешения для регистрации приложения. Например, если вы строите веб-aPI для доступа к Azure Data Explorer и хотите проверить подлинность с помощью учетных данных пользователя, *вызывая* ваш API.  

1. В **лезвии разрешений API** выберите **Добавление разрешения.**
1. Выберите **A- ИП, которые использует моя организация**. Поиск и выберите **Azure Data Explorer.**

    ![Добавление разрешения API-изврята данных Лазурных данных](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. В **делегированных разрешениях**выберите **user_impersonation** поле и **добавьте разрешения**

    ![Выберите делегированные разрешения с олицетворением пользователя](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>Предоставить службе основной доступ к базе данных Azure Data Explorer

Теперь, когда создана основная регистрация приложений службы, необходимо предоставить соответствующий основной доступ к базе данных Azure Data Explorer. 

1. В [веб-узи](https://dataexplorer.azure.com/)подключитесь к базе данных и откройте вкладку запроса.

1. Выполните следующую команду:

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    Пример:
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    Последний параметр — строка, которая отображается в виде заметок при запросе ролей, связанных с базой данных.
    
    > [!NOTE]
    > После создания регистрации приложения может потребоваться несколько минут, пока Azure Data Explorer не сможет ссылаться на него. Если при выполнения команды вы получаете ошибку, которую приложение не найдено, подождите и повторите попытку.

Для получения дополнительной информации [ingestion permissions](kusto/api/netfx/kusto-ingest-client-permissions.md) [см.](kusto/management/security-roles.md)  

## <a name="using-application-credentials-to-access-a-database"></a>Использование учетных данных приложений для доступа к базе данных

Используйте учетные данные приложения для программного доступа к базе данных с помощью [клиентской библиотеки Azure Data Explorer.](kusto/api/netfx/about-kusto-data.md)

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
   > Укажите идентификатор приложения и ключ регистрации приложения (основной услуги), созданный ранее.

Для получения дополнительной информации см. [аутентификации с Azure AD для доступа КВИП данных Azure и](kusto/management/access-control/how-to-authenticate-with-aad.md) [использование Azure Key Vault с веб-приложением .NET Core.](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app)

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="invalid-resource-error"></a>Ошибка недействительных ресурсов

Если приложение используется для проверки подлинности пользователей или приложений для доступа к Azure Data Explorer, необходимо настроить делегированные разрешения для приложения службы Azure Data Explorer. Объявить, что ваше приложение может проверить подлинность пользователей или приложений для доступа к Azure Data Explorer. Невыполнение этого решения приведет к ошибке, аналогичной следующему, когда предпринимается попытка проверки подлинности:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

Вам нужно будет следовать инструкциям по [настройке делегированных разрешений для приложения службы Azure Data Explorer.](#configure-delegated-permissions-for-the-application-registration)

### <a name="enable-user-consent-error"></a>Включить ошибку согласия пользователя

Администратор-арендатор Azure AD может ввести политику, которая не позволяет пользователям-арендаторам давать согласие на приложения. Эта ситуация приведет к ошибке, аналогичной следующему, когда пользователь пытается войти в приложение:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

Вам нужно связаться с администратором Azure AD, чтобы дать согласие всем пользователям в арендаторе или включить согласие пользователя для вашего конкретного приложения.

## <a name="next-steps"></a>Следующие шаги

* Посмотреть [строки соединения Kusto](kusto/api/connection-strings/kusto.md) для списка поддерживаемых строк соединения.
