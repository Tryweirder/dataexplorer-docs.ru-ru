---
title: MS-TDS с Azure Active Directoryом Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается MS-TDS с Azure Active Directory в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 01/02/2019
ms.openlocfilehash: 5511155eaa131c85a49a2082322ad95fcd022418
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862022"
---
# <a name="ms-tds-with-azure-active-directory"></a>MS-TDS с Azure Active Directory

## <a name="aad-user-authentication"></a>Проверка подлинности пользователя AAD

Клиенты SQL, поддерживающие проверку подлинности пользователя AAD, можно использовать с Kusto.

### <a name="net-sql-client-user"></a>Клиент SQL .NET (пользователь)

Например, для интегрированного AAD:
```csharp
    var csb = new SqlConnectionStringBuilder()
    {
        InitialCatalog = "mydatabase",
        Authentication = SqlAuthenticationMethod.ActiveDirectoryIntegrated,
        DataSource = "mykusto.kusto.windows.net"
    };
```

Kusto поддерживает проверку подлинности с уже полученным маркером доступа:
```csharp
    var csb = new SqlConnectionStringBuilder()
    {
        InitialCatalog = "mydatabase",
        DataSource = "mykusto.kusto.windows.net"
    };
    using (var connection = new SqlConnection(csb.ToString()))
    {
        connection.AccessToken = accessToken;
        await connection.OpenAsync();
    }
```

### <a name="jdbc-user"></a>JDBC (пользователь)

```java
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.Statement;
import com.microsoft.sqlserver.jdbc.SQLServerDataSource;
import com.microsoft.aad.adal4j.*;

public class Sample {
  public static void main(String[] args) throws Exception {
    AuthenticationResult authenticationResult = futureAuthenticationResult.get();
    SQLServerDataSource ds = new SQLServerDataSource();
    ds.setServerName("<your cluster DNS name>");
    ds.setDatabaseName("<your database name>");
    ds.setHostNameInCertificate("*.kusto.windows.net"); // Or appropriate regional domain.
    ds.setAuthentication("ActiveDirectoryIntegrated");
    try (Connection connection = ds.getConnection();
         Statement stmt = connection.createStatement();) {
      ResultSet rs = stmt.executeQuery("<your T-SQL query>");
      /*
      Read query result.
      */
    } catch (Exception e) {
      System.out.println();
      e.printStackTrace();
    }
  }
}
```

## <a name="aad-application-authentication"></a>Проверка подлинности приложения AAD

Приложение AAD, подготовленное для Kusto, может использовать клиентские библиотеки SQL, поддерживающие AAD для подключения к Kusto. Дополнительные сведения о приложениях AAD см. [в разделе Создание приложения AAD](../../management/access-control/how-to-provision-aad-app.md) .

### <a name="net-sql-client-application"></a>Клиент SQL .NET (приложение)

При условии, что подготовлено приложение AAD с *аппликатионклиентид* и *ApplicationKey* и ему предоставлены разрешения на доступ к базе данных *DatabaseName* в кластере *ClusterDnsName*, в следующем примере показано, как использовать клиент .NET SQL для запросов из этого приложения AAD.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;
using System.Data;
using System.Data.SqlClient;

namespace Sample
{
  class Program
  {
    private static async Task<string> ObtainToken()
    {
      var authContext = new AuthenticationContext(
        // Can also use tenant ID.
        "https://login.microsoftonline.com/<your AAD tenant name>");
      var applicationCredentials = new ClientCredential(
        "<your application client ID>",
        "<your application key>");
      var result = await authContext.AcquireTokenAsync(
        "https://<your cluster DNS name>",
        applicationCredentials);
      return result.AccessToken;
    }

    private static async Task QuerySample()
    {
      var csb = new SqlConnectionStringBuilder()
      {
        InitialCatalog = "<your database name>",
        DataSource = "<your cluster DNS name>"
      };
      using (var connection = new SqlConnection(csb.ToString()))
      {
        connection.AccessToken = await ObtainToken();
        await connection.OpenAsync();
        using (var command = new SqlCommand(
          "<your T-SQL query>",
          connection))
        {
          var reader = await command.ExecuteReaderAsync();
          /*
          Read query result.
          */
        }
      }
    }
  }
}
```

### <a name="jdbc-application"></a>JDBC (приложение)

```java
import java.sql.*;
import com.microsoft.sqlserver.jdbc.*;
import com.microsoft.aad.adal4j.*;

public class Sample {
  public static void main(String[] args) throws Throwable {
    ExecutorService service = Executors.newFixedThreadPool(1);
    // Can also use tenant name.
    String url = "https://login.microsoftonline.com/<your AAD tenant ID>";
    AuthenticationContext authenticationContext =
      new AuthenticationContext(url, false, service);
    ClientCredential  clientCredential = new ClientCredential(
      "<your application client ID>",
      "<your application key>");
    Future<AuthenticationResult> futureAuthenticationResult =
      authenticationContext.acquireToken(
        "https://<your cluster DNS name>",
        clientCredential,
        null);
    AuthenticationResult authenticationResult = futureAuthenticationResult.get();
    SQLServerDataSource ds = new SQLServerDataSource();
    ds.setServerName("<your cluster DNS name>");
    ds.setDatabaseName("<your database name>");
    ds.setAccessToken(authenticationResult.getAccessToken());
    connection = ds.getConnection();
    statement = connection.createStatement();
    ResultSet rs = statement.executeQuery("<your T-SQL query>");
    /*
    Read query result.
    */
  }
}
```