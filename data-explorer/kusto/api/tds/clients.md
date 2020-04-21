---
title: Клиенты MS-TDS и Kusto - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны клиенты MS-TDS и Kusto в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 233eec65c14d76b25b76cc85c7ddd190e5171dfe
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523500"
---
# <a name="ms-tds-clients-and-kusto"></a>Клиенты MS-TDS и Кусто

Kusto реализует tDS соответствующие конечные точки для клиентов MS-S'L. Поскольку совместимость находится на уровне протокола, любая библиотека или приложение, которые могут быть подключены к базе данных S'L Azure с аутентификацией Active Directory Azure, будет работать с сервером Kusto, ортогоонально работать с операционной системой или использовать время выполнения. Просто используйте доменное имя сервера Kusto, как будто это был сервер S'L Azure.

На уровне языка S'L Компания Kusto реализует подмножество T-S'L и подмножество эмуляции серверов S'L. Ознакомиться с некоторыми основными различиями между реализацией T-S'S l и Kusto [узнайте о некоторых](./sqlknownissues.md) основных отличиях от реализации T-S'SL и Kusto.

## <a name="net-sql-client"></a>Клиент .NET СЗЛ

Kusto поддерживает активную аутентификацию Azure для клиентов S'L.

Для получения подробной информации [см. .NET S'L Клиент (проверка подлинности пользователей)](./aad.md#net-sql-client-user) и [.NET S'L Клиент (проверка подлинности приложений)](./aad.md#net-sql-client-application)



## <a name="jdbc"></a>JDBC

Драйвер Microsoft JDBC может использоваться для подключения к Kusto с помощью аутентификации AAD.

Сделать приложение, чтобы использовать одну из версий *mssql-jdbc* JAR и *adal4j* JAR и все их dependecies. Пример:

```s
mssql-jdbc-7.0.0.jre8.jar
adal4j-1.6.3.jar
accessors-smart-1.2.jar
activation-1.1.jar
asm-5.0.4.jar
commons-codec-1.11.jar
commons-lang3-3.5.jar
gson-2.8.0.jar
javax.mail-1.6.1.jar
jcip-annotations-1.0-1.jar
json-smart-2.3.jar
lang-tag-1.4.4.jar
nimbus-jose-jwt-6.5.jar
oauth2-oidc-sdk-5.64.4.jar
slf4j-api-1.7.21.jar
```

Сделать прибор для использования класса драйвера JDBC`com.microsoft.sqlserver.jdbc.SQLServerDriver`

Используйте строку соединения, как:

```s
jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword
```

Если вы хотите использовать AAD интегрированный auth режим заменить *ActiveDirectoryPassword* с *ActiveDirectoryIntegrated*

Для получения более подробной информации [см. JDBC (проверка подлинности пользователей)](./aad.md#jdbc-user) и [JDBC (аутентификация приложений)](./aad.md#jdbc-application)

## <a name="odbc"></a>ODBC

Приложения, поддерживающие ODBC, могут подключаться к Kusto.

Создание источника данных ODBC:
1. Запуск администратора источника данных ODBC.
2. Создание нового источника `Add`данных (кнопка).
3. Выберите `ODBC Driver 17 for SQL Server`.
3. Укажите имя источника данных и укажите `Server` имя кластера Kusto в поле, например.`mykusto.kusto.windows.net`
4. Выберите `Active Directory Integrated` опцию аутентификации.
5. Следующая вкладка позволяет выбрать базу данных.
7. Можно оставить по умолчанию для всех других параметров в следующих вкладок.
8. `Finish`кнопка открывает сводный диалог источника данных, где соединение может быть проверено.
9. Источник ODBC теперь можно использовать с приложениями.

Если приложение ODBC может принимать строку соединения вместо или в дополнение к DSN, используйте следующую строку соединения:
```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
```

Некоторые приложения ODBC не очень хорошо работают с https://docs.microsoft.com/sql/relational-databases/native-client/features/using-large-value-types?view=sql-server-2017#sql-server-native-client-odbc-driver типом NVARCHAR (MAX) (подробнее см. Общим обходным решением, используемым для такого приложения, является отливка возвращенных данных в NVARCHAR (n), с некоторым значением для n, например NVARCHAR (4000). Такой обходной путь не будет работать для Kusto, так как Kusto имеет только один тип строки, а для клиентов S'L он кодируется как NVARCHAR (MAX). Kusto предлагает различные обходные пути для таких приложений. Можно настроить Kusto для кодирования всех строк как NVARCHAR (n) через строку соединения. Поле языка в строке соединения может быть использовано для `language@OptionName1:OptionValue1,OptionName2:OptionValue2`определения параметров настройки в формате: . 

Например, следующая строка соединения поручит Kusto кодировать строки как NVARCHAR (8000):
```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated,Language=any@MaxStringSize:8000"
```

### <a name="powershell"></a>PowerShell

Пример скрипта PowerShell, используюго драйвер ODBC:

```powershell
$conn = [System.Data.Common.DbProviderFactories]::GetFactory("System.Data.Odbc").CreateConnection()
$conn.ConnectionString = "Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
$conn.Open()
$conn.GetSchema("Tables")
$conn.Close()  

```



## <a name="linqpad"></a>LINQPad

Можно использовать приложения Linq с Kusto, подключившись к Kusto, как это сервер S'L.
ДЛЯ изучения совместимости Linq можно использовать LIN-Pad. Он также может быть использован для просмотра Kusto и для выполнения запросов S'L.
LIN-Pad является рекомендуемым инструментом для изучения конечных точек Kusto TDS (S'L).

Подключайтесь, как вы подключитесь к серверу Microsoft S'L. Обратите внимание, что LIN-Pad поддерживает активную проверку подлинности каталога.

1. Нажмите кнопку `Add connection`.
2. Выберите `Build data context automatically`.
3. Выберите драйвер `Default (LINQ to SQL)`LIN'Pad .
4. Как поставщик `SQL Azure`выбрать .
5. Для сервера укажите название кластера Kusto, например.`mykusto.kusto.windows.net`
6. Для входа можно `Windows Authentication (Active Directory)`выбрать .
7. Нажмите `Test` на кнопку, чтобы проверить подключение.
8. Нажмите `OK` на кнопку. Окно браузера отображает представление дерева с базами данных.
9. Вы можете просматривать базы данных, таблицы и столбцы.
10. В окне запроса можно выполнить запросы s'L. Укажите язык S'L и выберите подключение к базе данных.
11. В окне запроса также можно запускать запросы LIN. Например, нажмите правой кнопкой мыши на столе в окне браузера. Выберите `Count` вариант. Пусть работает.

## <a name="azure-data-studio-134-and-above"></a>Студия данных Azure (1.3.4 и выше)

1. Новое соединение.
2. Выберите тип `Microsoft SQL Server`соединения: .
3. Указать название кластера Kusto как имя сервера, например.`mykusto.kusto.windows.net`
4. Выберите тип `Azure Active Directory - Universal with MFA support`проверки подлинности: .
5. Укажите учетную запись, представленную в `myname@contoso.com` AAD, например (Добавить учетную запись с первого раза).
6. Сборщик базы данных может быть использован для выбора базы данных.
7. `Connect`кнопка приведет вас к панели мониторинга базы данных.
8. Нажмите право езду на соединение и выберите, `New Query` чтобы открыть вкладку запроса или нажмите на `New Query` задачу на панели мониторинга.

## <a name="power-bi-desktop"></a>Power BI Desktop

Подключайтесь, как вы подключитесь к базе данных S'L Azure.

1. В `Get Data` `More`выборе `Azure` , то и тогда`Azure SQL Database`
2. Укажите имя сервера Kusto, например.`mykusto.kusto.windows.net`
3. Используйте опцию "Прямой кекери".
4. Выберите `Microsoft account` аутентификацию (не) `Windows`и нажмите кнопку `sign in`.
5. Сборщик показывает доступные базы данных. Продолжайте так же, как вы сделали бы с реальным сервером S'L.

## <a name="excel"></a>Excel

Подключайтесь, как вы подключитесь к базе данных S'L Azure.

1. В `Data` вкладке, `Get Data`, `From Azure``From Azure SQL Database`
2. Укажите имя сервера Kusto, например.`mykusto.kusto.windows.net`
3. Выберите `Microsoft account` аутентификацию (не) `Windows`и нажмите кнопку `sign in`.
4. После входного `Connect`в, нажмите кнопку .
5. Сборщик показывает доступные базы данных. Продолжайте так же, как вы сделали бы с реальным сервером S'L.

## <a name="tableau"></a>Tableau

1. Создайте источник данных [ODBC](./clients.md#odbc) ODBC, см.
2. Подключайтесь через `Other Databases (ODBC)`.
3. Выберите источник данных ODBC в `DSN`.
4. Используйте `Connect` кнопку для установления соединения.
5. После `Sign In` того, как кнопка доступна, войти в Kusto.

## <a name="dbeaver-533-and-above"></a>DBeaver (5.3.3 и выше)

Направите DBeaver для обработки наборов результатов таким образом, чтобы они соответствовали:

1. В `Window` меню `Preferences`выберите .
2. В `Editors` разделе `Data Editor`выберите .
3. Убедитесь, что опция `Refresh data on next page reading` проверена.

Создание соединения с базой данных Kusto:

1. Создайте новое соединение`Database` базы `New Connection` данных (меню и опция).
2. Ищите `Azure` `Azure SQL Database`и выберите . Нажмите кнопку `Next`.
3. Указать хост, `mykusto.kusto.windows.net` например, базу `mydatabase`данных, например. Не оставляйте мастера в качестве имени базы данных. Kusto требует подключения к определенной базе данных.
4. Для проверки `Active Directory - Password`подлинности выберите вариант.
5. Укажите учетные данные активного пользователя `myname@contoso.com` каталога, например, и соответствующий пароль для этого пользователя.
6. Нажмите, `Test Connection …` чтобы проверить детали соединения являются правильными.

## <a name="microsoft-sql-server-management-studio-v18x"></a>Студия управления серверами Microsoft S'L (v18.x)

1. В `Object Explorer` `Connect`, `Database Engine`.
2. Указать название кластера Kusto как имя сервера, например.`mykusto.kusto.windows.net`
3. Используйте `Active Directory - Integrated` опцию для проверки подлинности.
4. Нажмите кнопку `Options`.
5. В `Connect to database` комбо вы можете просматривать `Browse Server` доступные базы данных с помощью опции.
6. Нажмите, `Yes` чтобы продолжить просмотр.
7. Диалог отображает представление дерева со всеми доступными базами данных. Можно нажать на один, чтобы продолжить подключение к базе данных.
8. Кроме того, `Connect to database` в комбо, можно выбрать `default`. Нажмите кнопку `Connect`. Object Explorer будет отображать все базы данных.
9. Просмотр объектов базы данных через SSMS пока не поддерживается, так как SSMS использует коррелирующие подзапросы для просмотра схемы базы данных. Коррелированные подзапросы не поддерживаются Kusto, [см. коррелированные подзапросы.](./sqlknownissues.md#correlated-sub-queries)
10. Нажмите на базу данных. Нажмите `New Query` опцию, чтобы открыть окно запроса.
11. Можно выполнять пользовательские запросы из окна запроса.

## <a name="matlab-via-jdbc"></a>MATLAB (через JDBC)

Добавьте необходимые JAR-файлы в переднюю часть статического classpath MATLAB. Для этого создайте файл *"javaclasspath.txt"* в каталоге предпочтений. Выполнить следующую команду в командном окне Matlab: 

``` s
edit(fullfile(prefdir,'javaclasspath.txt'))
```

И добавьте полные пути к требуемым JAR-файлам: 

``` s
<before>
c:\full\path\to\accessors-smart-1.2.jar
c:\full\path\to\activation-1.1.jar
c:\full\path\to\adal4j-1.6.3.jar
c:\full\path\to\asm-5.0.4.jar
c:\full\path\to\commons-codec-1.11.jar
c:\full\path\to\commons-lang3-3.5.jar
c:\full\path\to\gson-2.8.0.jar
c:\full\path\to\javax.mail-1.6.1.jar
c:\full\path\to\jcip-annotations-1.0-1.jar
c:\full\path\to\json-smart-2.3.jar
c:\full\path\to\lang-tag-1.4.4.jar
c:\full\path\to\mssql-jdbc-7.0.0.jre8.jar
c:\full\path\to\nimbus-jose-jwt-6.5.jar
c:\full\path\to\oauth2-oidc-sdk-5.64.4.jar
c:\full\path\to\slf4j-api-1.7.21.jar
```

> Вам действительно нужно *<, прежде чем*> в верхней части, чтобы добавить эти файлы в передней части classpath. Далее заменить "c:'full'path'to" с фактическими полными путями к этим файлам. 

Перезапустите MATLAB, чтобы убедиться, что эти классы на самом деле загружены.

Перед попыткой подключения выполняемтся следующие команды (окно команды MATLAB):

``` java
java.lang.System.clearProperty('javax.xml.transform.TransformerFactory')
```

> Это сбрасывает *TransformerFactory* по умолчанию (MATLAB обычно перегружает это с Саксоном, но это несовместимо с ADAL4J).

Для подключения к конечной точке Kusto TDS отправьте следующую команду (окно команды MATLAB):

```s
conn = database('<<KUSTO_DATABASE>>','<<AAD_USER>>','<<USER_PWD>>','com.microsoft.sqlserver.jdbc.SQLServerDriver',['jdbc:sqlserver://<<MYCLUSTER>>.kusto.windows.net:1433;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword;database='])
 ```

> Это правильно здесь, что это просто заканчивается *"база данных",* а затем не имя, "база данных" функция будет автоматически придатить первый вход (имя базы данных) к этой строке.
> Если вы хотите использовать AAD интегрированный auth режим заменить *ActiveDirectoryPassword* с *ActiveDirectoryIntegrated*

Проверьте соединение и запустите пример запроса. Отправить умереть следующие команды (MATLAB командное окно):

```s
data = select(conn, 'SELECT * FROM <<KUSTO_TABLE>>')
data
```

> Заменить *KUSTO_TABLE* существующей таблицей в Кусто



## <a name="sending-t-sql-queries-over-the-rest-api"></a>Отправка запросов по API REST

[API Kusto REST](../rest/index.md) может принимать и выполнять запросы T-S'L.
Для этого отправьте запрос в конечную точку запроса `csl` с набором свойств в текст самого запроса T-S'L, а также [свойство](../netfx/request-properties.md) `OptionQueryLanguage` запроса, установленное на значение. `sql`