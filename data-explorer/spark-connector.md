---
title: Используйте разъем Azure Data Explorer для Apache Spark для перемещения данных между кластерами Azure Data Explorer и Spark.
description: В этой теме показано, как перемещать данные между кластерами Azure Data Explorer и Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: 0c8d1add5f5f72ad96475df67ec2ff20bc9364c0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81495527"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Разъем Azure Data Explorer для Apache Spark

[Apache Spark](https://spark.apache.org/) — это единый аналитический движок для крупномасштабной обработки данных. Azure Data Explorer — это быстрая, полностью управляемая служба анализа данных для анализа больших объемов данных в режиме реального времени. 

Разъем Azure Data Explorer для Spark — это [проект с открытым исходным кодом,](https://github.com/Azure/azure-kusto-spark) который может работать на любом кластере Spark. Он реализует источник данных и поглотитель данных для перемещения данных через кластеры Azure Data Explorer и Spark. Используя Azure Data Explorer и Apache Spark, можно создавать быстрые и масштабируемые приложения, ориентированные на сценарии, ориентированные на данные. Например, машинное обучение (ML), Extract-Transform-Load (ETL) и Log Analytics. С разъемом Azure Data Explorer становится действительным хранилищем данных для стандартных операций источника и поглотителей Spark, таких как запись, чтение и запись Stream.

Вы можете написать в Azure Data Explorer в режиме пакетной или потоковой передачи. Чтение из Azure Data Explorer поддерживает обрезку столбцов и предикат pushdown, которая фильтрует данные в Azure Data Explorer, уменьшая объем переданных данных.

В этой теме описывается, как установить и настроить разъем Azure Data Explorer Spark и переместить данные между кластерами Azure Data Explorer и Apache Spark.

> [!NOTE]
> Хотя некоторые приведенные ниже примеры относятся к кластеру [Azure Databricks](https://docs.azuredatabricks.net/) Spark, разъем Azure Data Explorer Spark не принимает прямой зависимости от Databricks или любого другого дистрибутива Spark.

## <a name="prerequisites"></a>Предварительные требования

* [Создание кластера и базы данных Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) 
* Создание кластера Spark
* Установите библиотеку разъема Azure Data Explorer:
    * Предварительно построенные библиотеки для [Spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Maven репо](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3.x](https://maven.apache.org/download.cgi) установлен

> [!TIP]
> Версии 2.3.x также поддерживаются, но могут потребовать некоторых изменений в зависимостях pom.xml.

## <a name="how-to-build-the-spark-connector"></a>Как построить разъем Spark

> [!NOTE]
> Это необязательный шаг. Если вы используете предварительно построенные библиотеки, перейдите на [настройку кластера Spark.](#spark-cluster-setup)

### <a name="build-prerequisites"></a>Создание предпосылок

1. Установите библиотеки, перечисленные в [зависимостях,](https://github.com/Azure/azure-kusto-spark#dependencies) включая следующие [библиотеки Kusto Java SDK:](kusto/api/java/kusto-java-client-library.md)
    * [Клиент данных Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Кусто Ingest Клиент](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Обратитесь к [этому источнику](https://github.com/Azure/azure-kusto-spark) для создания разъема Spark.

1. Для приложений Scala/Java, использующих определения проекта Maven, свяжите приложение со следующим артефактом (последняя версия может отличаться):
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>Команды сборки

Для создания JAR-файла и выполнения всех тестов выполните команду:

```
mvn clean package
```

Чтобы построить банку, запустите все тесты и установите банку в локальное репозиторий Maven:

```
mvn clean install
```

Для получения дополнительной информации [см.](https://github.com/Azure/azure-kusto-spark#usage)

## <a name="spark-cluster-setup"></a>Установка кластера Spark

> [!NOTE]
> Рекомендуется использовать последний разъем разъем Azure Data Explorer Spark при выполнении следующих шагов.

1. Настроите следующие настройки кластера Spark на основе кластера Azure Databricks с использованием Spark 2.4.4 и Scala 2.11:

    ![Настройки кластера Databricks](media/spark-connector/databricks-cluster.png)
    
1. Установите последнюю библиотеку искры-кусто-разъем от Maven:
    
    ![Библиотеки](media/spark-connector/db-libraries-view.png) ![импорта Выберите Spark-Kusto-Connector](media/spark-connector/db-dependencies.png)

1. Убедитесь, что все необходимые библиотеки установлены:

    ![Проверка установленных библиотек](media/spark-connector/db-libraries-view.png)

1. Для установки с помощью файла JAR убедитесь, что были установлены дополнительные зависимости:

    ![Добавление зависимостей](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Аутентификация

Разъем Azure Data Explorer Spark позволяет аутентифицировать с помощью Active Directory (Azure AD) с помощью одного из следующих методов:
* [Приложение Azure AD](#azure-ad-application-authentication)
* [Токен доступа Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [Проверка подлинности устройств](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (для непроизводственных сценариев)
* [Убежище ключей Azure](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) для доступа к ресурсу Key Vault, установки пакета azure-keyvault и предоставления учетных данных приложений.

### <a name="azure-ad-application-authentication"></a>Аутентификация приложения Azure AD

Аутентификация приложения Azure AD является самым простым и распространенным методом проверки подлинности и рекомендуется для разъема Azure Data Explorer Spark.

|Свойства  |Описание  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Идентификатор приложения Azure AD (клиент).      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Орган по аутентификации Azure AD. Идентификатор каталога AD Azure AD.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Ключ приложения Azure AD для клиента.     |

### <a name="azure-data-explorer-privileges"></a>Привилегии Azure Data Explorer

Предоставь следующие привилегии в кластере Azure Data Explorer:

* Для чтения (источника данных) интатора Azure AD должна иметь привилегии *просмотра* в целевой базе данных или привилегии *администрирования* в целевой таблице.
* Для записи (поглотителя данных) интатор Azure AD должен иметь привилегии *ingestor* в целевой базе данных. Он также должен иметь привилегии *пользователя* в целевой базе данных для создания новых таблиц. Если целевая таблица уже существует, необходимо настроить привилегии *админа* в целевой таблице.
 
Для получения дополнительной информации о главных ролях Azure Data Explorer [см.](kusto/management/access-control/role-based-authorization.md) Для управления ролями [security roles management](kusto/management/security-roles.md)безопасности см.

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Раковина искры: письмо в Azure Data Explorer

1. Настройка параметров раковины:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Запись Spark DataFrame в кластер Azure Data Explorer в виде пакета:

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey)
      .option(KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .option(KustoSinkOptions.KUSTO_TABLE_CREATE_OPTIONS, "CreateIfNotExist")
      .mode(SaveMode.Append)
      .save()  
    ```
    
   Или используйте упрощенный синтаксис:
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. Запись потоковых данных:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
        
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Источник искры: чтение от Исследователя данных Azure

1. При [чтении небольших объемов данных](kusto/concepts/querylimits.md)определите запрос данных:

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. Дополнительно: Если **вы** предоставляете переходное хранилище капли (а не Azure Data Explorer), то капли создаются под ответственностью вызывающегося абонента. Это включает в себя предоставление хранилища, вращающиеся клавиши доступа и удаляние переходных артефактов. 
    Модуль KustoBlobStorageUtils содержит функции помощника для удаляния капли на основе либо учетных записей и координат контейнера и учетных данных учетной записи, либо полного URL SAS с разрешениями на запись, чтение и список. Когда соответствующий RDD больше не нужен, каждая транзакция хранит переходные артефакты blob в отдельном каталоге. Этот каталог фиксируется как часть информационных журналов считывания транзакций, сообщаемых на узлах Драйвера Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    В приведенном выше примере доступ к Key Vault не используется с помощью интерфейса разъема; используется более простой метод использования секретов Databricks.

1. Читайте от Azure Data Explorer.

    * Если **вы** предоставляете переходное хранилище капли, прочитайте из Azure Data Explorer следующее:

        ```scala
         val conf3 = Map(
              KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
              KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
              KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
        val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
        
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

    * Если **Azure Data Explorer** предоставляет переходное хранилище, прочитайте из Azure Data Explorer следующее:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>Следующие шаги

* Узнайте больше о [разъединении искры Azure Data Explorer](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Пример кода для Java и Python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
