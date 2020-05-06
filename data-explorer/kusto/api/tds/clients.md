---
title: Клиенты MS-TDS и Kusto — Azure обозреватель данных
description: В этой статье описываются клиенты MS-TDS и Kusto в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 5e2de0c29c58959ce683518b03bef9164fa9543c
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799634"
---
# <a name="ms-tds-clients-and-azure-data-explorer"></a>Клиенты MS-TDS и Azure обозреватель данных

Обозреватель данных Azure реализует конечные точки, совместимые с TDS, для клиентов MS-SQL. Совместимость зависит от уровня протокола. Любая библиотека или приложение, которые могут подключаться к базе данных SQL Azure с помощью проверки подлинности Azure Active Directory (Azure AD), будут работать с сервером Azure обозреватель данных. Таким образом, можно использовать доменное имя сервера, как и SQL Azure Server.

Обозреватель данных Azure реализует подмножество T-SQL и подмножество эмуляции SQL Server. Дополнительные сведения см. в статье [Известные проблемы](./sqlknownissues.md) при различиях между реализацией SQL Server T-SQL и Azure обозреватель данных.

## <a name="net-sql-client"></a>Клиент SQL .NET

Azure обозреватель данных поддерживает проверку подлинности Azure AD для клиентов SQL. Дополнительные сведения см. в статьях [клиент SQL .NET (проверка подлинности пользователя)](./aad.md#net-sql-client-user) и [клиент SQL .NET (проверка подлинности приложения)](./aad.md#net-sql-client-application) .

## <a name="jdbc"></a>JDBC

Драйвер Microsoft JDBC можно использовать для подключения к Azure обозреватель данных с проверкой подлинности Azure AD.

Создайте приложение для использования одной из версий JAR *-* и *adal4j* JAR, а также всех их зависимостей.
Например, примененная к объекту директива

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

Создайте приложение для использования класса драйвера JDBC *com. Microsoft. SqlServer. JDBC. SQLServerDriver*.

Используйте строку подключения, как в следующем примере.

```s
jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword
```

> [!NOTE]
> Если вы хотите использовать режим встроенной проверки подлинности Azure Active Directory, замените *ActiveDirectoryPassword* на *ActiveDirectoryIntegrated*. Дополнительные сведения см. в статьях о [JDBC (проверка подлинности пользователя)](./aad.md#jdbc-user) и [JDBC (проверка подлинности приложения)](./aad.md#jdbc-application).

## <a name="odbc"></a>ODBC

Приложения, поддерживающие ODBC, могут подключаться к Azure обозреватель данных.

Создайте источник данных ODBC.

1. Запустите администратор источников данных ODBC.
2. Выберите **Добавить** , чтобы создать новый источник данных, и установите *ODBC Driver 17 для SQL Server*.
3. Присвойте источнику данных имя и укажите имя кластера Azure обозреватель данных в поле " **сервер** ". Например, *mykusto.kusto.Windows.NET*.
4. Установите **Active Directory Integrated**для параметра Authentication (проверка подлинности).
5. Нажмите кнопку **Далее** , чтобы задать базу данных.
7. Можно оставить значения по умолчанию для всех остальных параметров на следующих вкладках.
8. Нажмите кнопку **Готово** , чтобы открыть окно Сводка источника данных, в котором можно проверить подключение.

Теперь можно использовать источник данных ODBC с приложениями.

Если приложение ODBC может принимать строку подключения, а не или в дополнение к DSN, используйте следующую команду.

```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
```

Некоторые приложения ODBC плохо работают с `NVARCHAR(MAX)` типом. Для получения дополнительной информации см. https://docs.microsoft.com/sql/relational-databases/native-client/features/using-large-value-types?view=sql-server-2017#sql-server-native-client-odbc-driver. 

Распространенный обходной путь заключается в приведении возвращаемых данных к типу *nvarchar (n)* с некоторым значением n. Например, *nvarchar (4000)*. Однако такой обходной путь не будет работать для Azure обозреватель данных, так как Azure обозреватель данных имеет только один строковый тип и для клиентов SQL, закодированный как *nvarchar (max)*.

Azure обозреватель данных предлагает другое решение. Вы можете настроить обозреватель данных Azure для кодирования всех строк как *nvarchar (n)* через строку подключения. Поле Language (язык) в строке подключения можно использовать для указания параметров настройки в формате, * language@OptionName1:OptionValue1OptionName2: OptionValue2*.

Например, следующая строка подключения попросит обозреватель данных Azure закодировать строки как *nvarchar (8000)*.

```s
"Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated,Language=any@MaxStringSize:8000"
```

### <a name="powershell"></a>PowerShell

Ниже приведен пример сценария PowerShell, использующего драйвер ODBC.

```powershell
$conn = [System.Data.Common.DbProviderFactories]::GetFactory("System.Data.Odbc").CreateConnection()
$conn.ConnectionString = "Driver={ODBC Driver 17 for SQL Server};Server=mykustocluster.kusto.windows.net;Database=mykustodatabase;Authentication=ActiveDirectoryIntegrated"
$conn.Open()
$conn.GetSchema("Tables")
$conn.Close()  
```

## <a name="linqpad"></a>LINQPad

Приложение LINQ можно использовать с обозреватель данных Azure, подключив его так же, как и SQL Server.
Используйте LINQPad для изучения совместимости LINQ и просмотра обозреватель данных Azure. Он также может выполнять SQL-запросы и является рекомендуемым средством для просмотра конечных точек Azure обозреватель данных TDS (SQL).

Подключитесь так же, как и в Microsoft SQL Server. LINQPad поддерживает проверку подлинности Active Directory.

1. Выберите команду **Добавить подключение**.
2. Задать **контекст данных сборки автоматически**.
3. Задайте драйвер LINQPad **по умолчанию (LINQ to SQL)**.
4. Задайте **SQL Azure**.
5. Для сервера укажите имя кластера Azure обозреватель данных. Например, *mykusto.kusto.Windows.NET*.
6. Настройте **проверку подлинности Windows (Active Directory)** для входа.
7. Выберите **проверить** , чтобы проверить подключение.
8. Щелкните **ОК**. В окне браузера отображается представление в виде дерева с базами данных.
9. Можно просматривать базы данных, таблицы и столбцы.
10. Запросы SQL можно выполнять в окне запроса. Укажите язык SQL и выберите соединение с базой данных.
11. Запросы LINQ можно также выполнять в окне запроса. Например, выберите таблицу в окне браузера. Выберите **количество**и разрешите выполнение.

## <a name="azure-data-studio-134-and-above"></a>Azure Data Studio (1.3.4 и выше)

Создать новое соединение.

1. Задайте для параметра Тип соединения значение **Microsoft SQL Server**.
2. Укажите имя кластера Azure обозреватель данных в качестве имени сервера. Например, *mykusto.kusto.Windows.NET*.
3. Задайте тип проверки подлинности **Azure Active Directory-Universal с поддержкой MFA**.
4. Укажите учетную запись, подготовленную в Azure AD. (например, *myname@contoso.com* ). Добавьте учетную запись в первый раз.
5. Выберите базу данных с помощью средства выбора базы данных.
6. Нажмите кнопку **Подключиться** , чтобы перейти на панель мониторинга базы данных и установить соединение.
7. Выберите **создать запрос** , чтобы открыть окно запроса, или выберите задачу **создать запрос** на панели мониторинга.

## <a name="power-bi-desktop"></a>Power BI Desktop;

Подключитесь так же, как и в SQL Azure базу данных.

1. В разделе **Получение данных**выберите **больше**.
2. Задайте **Azure**, а затем **базу данных SQL Azure**.
3. Укажите имя сервера обозреватель данных Azure. Например, *mykusto.kusto.Windows.NET*.
4. Выберите **DirectQuery**.
5. Задайте проверку подлинности **учетная запись Майкрософт** (не **Windows**) и выберите **Вход**.
6. В средстве выбора базы данных отображаются доступные базы данных. Продолжайте, как это делается, с реальным SQL Server.

## <a name="excel"></a>Excel

Подключитесь так же, как и в SQL Azure базу данных.

1. Выберите **получить данные** на вкладке **данные** , а затем — **из Azure** и **из базы данных SQL Azure**.
2. Укажите имя сервера обозреватель данных Azure. Например, *mykusto.kusto.Windows.NET*.
3. Задайте проверку подлинности **учетная запись Майкрософт** (не **Windows**) и выберите **Вход**.
5. В средстве выбора базы данных отображаются доступные базы данных. Продолжайте, как это делается, с реальным SQL Server.
4. После входа выберите **подключить**.
5. В средстве выбора базы данных отображаются доступные базы данных. Продолжайте, как это делается, с реальным SQL Server.

## <a name="tableau"></a>Tableau

Создайте источник данных ODBC. Дополнительные сведения см. в разделе [ODBC](./clients.md#odbc) .

1. Подключение через **другие базы данных (ODBC)**.
2. Задайте источник данных ODBC в **DSN**.
3. Выберите **Подключиться** , чтобы установить соединение.
4. Щелкните **войти**, после того как кнопка будет доступна, и войдите в Azure обозреватель данных.

## <a name="dbeaver-533-and-above"></a>Дбеавер (5.3.3 и выше)

Настройте Дбеавер для обработки результирующих наборов способом, совместимым с Azure обозреватель данных.

1. В меню **окно** выберите пункт **предпочтения** .
2. В разделе **редакторы** выберите **Редактор данных** .
3. Убедитесь, что **Обновление данных при чтении со следующей страницы** отмечено как.

Создайте подключение к базе данных Azure обозреватель данных.

1. В меню **база данных** выберите пункт **создать соединение** .
2. Найдите **Azure** и задайте **базу данных SQL Azure**. Выберите **Далее**.
3. Укажите узел. Например, *mykusto.kusto.Windows.NET*.
4. Укажите базу данных. Например, *MyDatabase*.

> [!WARNING]
> Не используйте в качестве имени базы данных *master* . Обозреватель данных Azure требуется подключение к определенной базе данных.

5. Задайте **Active Directory-Password** для *проверки подлинности*.
6. Укажите учетные данные пользователя Active Directory. *myname@contoso.com*Например, и задайте соответствующий пароль для этого пользователя.
7. Выберите **проверить подключение...** для проверки правильности сведений о подключении.

## <a name="microsoft-sql-server-management-studio-v18x"></a>Management Studio Microsoft SQL Server (V18. x)

1. Выберите **подключить**, а затем **ядро СУБД** в **обозревателе объектов**.
2. Укажите имя кластера Azure обозреватель данных в качестве имени сервера. Например, *mykusto.kusto.Windows.NET*.
3. Установите **Active Directory-Integrated** для проверки подлинности.
4. Выберите **Параметры**.
5. Выберите **Обзор сервера** в разделе **Подключение к базе данных** для просмотра доступных баз данных.
6. Выберите **Да** , чтобы продолжить просмотр.
7. В окне отображается древовидное представление со всеми доступными базами данных. Выберите один из них, чтобы подключиться к базе данных.
8. Можно также выбрать вариант **по умолчанию** в разделе **Подключение к базе данных**, а затем нажать кнопку **подключить**. В обозревателе объектов отобразятся все базы данных.

> [!NOTE]
> Просмотр объектов базы данных с помощью SSMS пока не поддерживается, так как SSMS использует коррелированные вложенные запросы для просмотра схемы базы данных.
> Коррелированные вложенные запросы не поддерживаются обозреватель данных Azure. Дополнительные сведения см. в разделе [коррелированные вложенные запросы](./sqlknownissues.md#correlated-sub-queries).

10. Выберите **создать запрос** , чтобы открыть окно запроса и задать свою базу данных.

В окне запроса можно выполнять пользовательские SQL запросы.

## <a name="matlab-via-jdbc"></a>MATLAB (через JDBC)

Добавьте необходимые JAR-файлы в начало статических классов подкаталогов MATLAB, создав файл *"жавакласспас. txt"* в каталоге предпочтений.

1. Выполните следующую команду в командном окне MATLAB.

``` s
edit(fullfile(prefdir,'javaclasspath.txt'))
```

2. Добавьте полные пути к необходимым JAR-файлам.

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

> [!NOTE]
> Вам потребуется <, **прежде чем**> в верхней части, чтобы эти файлы добавлялись в начало классов подкаталогов. Кроме того, замените *к:\фулл\пас\то* на фактические полные пути к этим файлам.

3. Перезапустите MATLAB, чтобы убедиться, что эти классы загружены.

4. Перед попыткой подключения выполните следующую команду (окно командной строки MATLAB).

``` java
java.lang.System.clearProperty('javax.xml.transform.TransformerFactory')
```

> [!NOTE]
> При этом **трансформерфактори** сбрасывается до значения по умолчанию (при этом MATLAB обычно перегружается с помощью **Саксон**, но это несовместимо с **ADAL4J**).

5. Подключитесь к конечной точке Azure обозреватель данных TDS с помощью следующей команды (окно командной строки MATLAB).

```s
conn = database('<<KUSTO_DATABASE>>','<<AAD_USER>>','<<USER_PWD>>','com.microsoft.sqlserver.jdbc.SQLServerDriver',['jdbc:sqlserver://<<MYCLUSTER>>.kusto.windows.net:1433;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword;database='])
 ```

> [!NOTE]
> Можно завершить с **"Database ="** и без значения. Функция *Database* автоматически добавит в эту строку первый вход, имя базы данных.
> Если вы хотите использовать режим встроенной проверки подлинности Azure Active Directory, замените *ActiveDirectoryPassword* на *ActiveDirectoryIntegrated*.

6. Проверьте подключение и выполните пример запроса. Отправьте следующие команды (окно командной строки MATLAB).

```s
data = select(conn, 'SELECT * FROM <<KUSTO_TABLE>>')
data
```

> [!NOTE]
> Замените *KUSTO_TABLE* существующей таблицей в обозреватель данныхе Azure.

## <a name="sending-t-sql-queries-over-the-rest-api"></a>Отправка запросов T-SQL по REST API

[REST API обозреватель данных Azure](../rest/index.md) может принимать и выполнять запросы T-SQL.

1. Отправьте запрос в конечную точку запроса со свойством **CSL** , установленным в текст запроса T-SQL.
2. Задайте для **[Свойства запроса](../netfx/request-properties.md)** **оптионкуерилангуаже** значение **SQL**.
