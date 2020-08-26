---
title: Используйте соединитель Azure обозреватель данных для Apache Spark перемещения данных между кластерами обозреватель данных и Spark Azure.
description: В этом разделе показано, как перемещать данные между кластерами Azure обозреватель данных и Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: maraheja
ms.service: data-explorer
ms.topic: how-to
ms.date: 7/29/2020
ms.openlocfilehash: 9eb10d9a87e8cf001b229d9c6c553e88ef355144
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875282"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Соединитель Azure обозреватель данных для Apache Spark

[Apache Spark](https://spark.apache.org/) — это единый аналитический механизм для крупномасштабной обработки данных. Azure обозреватель данных — это быстрая, полностью управляемая служба аналитики данных для анализа больших объемов данных в режиме реального времени. 

Соединитель Azure обозреватель данных для Spark — это [проект с открытым исходным кодом](https://github.com/Azure/azure-kusto-spark) , который может работать в любом кластере Spark. Он реализует источник данных и приемник данных для перемещения данных между кластерами Azure обозреватель данных и Spark. С помощью Azure обозреватель данных и Apache Spark можно создавать быстрые и масштабируемые приложения, предназначенные для сценариев, управляемых данными. Например, машинное обучение (ML), извлечение-преобразование-Загрузка (ETL) и Log Analytics. С помощью соединителя обозреватель данных Azure преобразуется в допустимое хранилище данных для стандартных операций источника и приемника Spark, таких как запись, чтение и Вритестреам.

Запись в обозреватель данных Azure можно выполнять в пакетном или потоковой режиме. Чтение из Azure обозреватель данных поддерживает очистку столбцов и предикаты включение, которые отфильтровывают данные в обозреватель данных Azure, уменьшая объем передаваемых данных.

В этом разделе описывается установка и Настройка соединителя Azure обозреватель данных Spark и перемещение данных между кластерами Azure обозреватель данных и Apache Spark.

> [!NOTE]
> Хотя некоторые из приведенных ниже примеров относятся к кластеру [Azure Databricks](https://docs.azuredatabricks.net/) Spark, соединитель Azure обозреватель данных Spark не принимает прямые зависимости от кирпичей данных или других дистрибутивов Spark.

## <a name="prerequisites"></a>Предварительные условия

* [Создание кластера Azure обозреватель данных и базы данных](create-cluster-database-portal.md) 
* Создание кластера Spark
* Установка библиотеки соединителей Azure обозреватель данных:
    * Предварительно созданные библиотеки для [Spark 2,4, Scala 2,11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Репозиторий Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* Установлен [Maven 3. x](https://maven.apache.org/download.cgi)

> [!TIP]
> версии 2.3. x также поддерживаются, но может потребоваться внести некоторые изменения в зависимости pom.xml.

## <a name="how-to-build-the-spark-connector"></a>Как создать соединитель Spark

> [!NOTE]
> Это необязательный шаг. Если вы используете предварительно созданные библиотеки, перейдите в раздел [Установка кластера Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Необходимые компоненты для сборки

1. Установите библиотеки, перечисленные в списке [зависимости](https://github.com/Azure/azure-kusto-spark#dependencies) , включая следующие библиотеки [SDK Kusto для Java](kusto/api/java/kusto-java-client-library.md) :
    * [Клиент данных Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Принимающий клиент Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Сведения о создании соединителя Spark см. в [этом источнике](https://github.com/Azure/azure-kusto-spark) .

1. Для приложений Scala/Java, использующих определения проектов Maven, свяжите приложение со следующим артефактом (последняя версия может отличаться):
    
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

Чтобы создать JAR-файл, выполните все тесты и установите JAR-файл в локальный репозиторий Maven:

```
mvn clean install
```

Дополнительные сведения см. в разделе [Использование соединителя](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Установка кластера Spark

> [!NOTE]
> При выполнении следующих действий рекомендуется использовать последнюю версию соединителя Azure обозреватель данных Spark.

1. Настройте следующие параметры кластера Spark на основе кластера Azure Databricks, используя Spark 2.4.4 и Scala 2,11:

    ![Параметры кластера "кирпичные данные"](media/spark-connector/databricks-cluster.png)
    
1. Установите последнюю версию библиотеки Spark-kusto-Connector из Maven:
    
    ![Импорт библиотек ](media/spark-connector/db-libraries-view.png) ![ выберите Spark-Kusto-Connector](media/spark-connector/db-dependencies.png)

1. Убедитесь, что установлены все необходимые библиотеки:

    ![Проверка установленных библиотек](media/spark-connector/db-libraries-view.png)

1. Для установки с помощью JAR-файла убедитесь, что установлены дополнительные зависимости:

    ![Добавление зависимостей](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Аутентификация

Соединитель Azure обозреватель данных Spark позволяет выполнять аутентификацию с помощью Azure Active Directory (Azure AD) одним из следующих способов.
* [Приложение Azure AD](#azure-ad-application-authentication) ;
* [Маркер доступа Azure AD](https://github.com/Azure/azure-kusto-spark/blob/master/docs/Authentication.md#direct-authentication-with-access-token) ;
* [Проверка подлинности устройства](https://github.com/Azure/azure-kusto-spark/blob/master/docs/Authentication.md#device-authentication) (для нерабочих сценариев)
* [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/master/docs/Authentication.md#key-vault) для доступа к ресурсу Key Vault, установите пакет Azure-keyvault и укажите учетные данные приложения.

### <a name="azure-ad-application-authentication"></a>Проверка подлинности приложения Azure AD

Проверка подлинности приложения Azure AD является самым простым и наиболее распространенным методом проверки подлинности и рекомендуется для соединителя Azure обозреватель данных Spark.

|Свойства  |Строка параметра  |Описание  |
|---------|---------|---------|
|**KUSTO_AAD_APP_ID**     |кустоаадаппид     |   Идентификатор приложения Azure AD (клиент).      |
|**KUSTO_AAD_AUTHORITY_ID**     |кустоаадаусоритид     |  Центр проверки подлинности Azure AD. Идентификатор каталога Azure AD (клиент).        |
|**KUSTO_AAD_APP_SECRET**    |кустоаадаппсекрет     |    Ключ приложения Azure AD для клиента.     |

> [!NOTE]
> Более старые версии API (меньше 2.0.0) имеют следующие имена: "Кустоаадклиентид", "Кустоклиентаадклиентпассворд", "Кустоаадаусоритид"

### <a name="azure-data-explorer-privileges"></a>Права обозреватель данных Azure

Предоставьте следующие права в кластере Azure обозреватель данных.

* Для чтения (источник данных) удостоверение Azure AD должно иметь права доступа *средства просмотра* в целевой базе данных или права *администратора* в целевой таблице.
* Для записи (приемника данных) удостоверение Azure AD должно иметь привилегии *приема* в целевой базе данных. Для создания новых таблиц у нее также должны быть привилегии *пользователя* в целевой базе данных. Если целевая таблица уже существует, необходимо настроить права *администратора* в целевой таблице.
 
Дополнительные сведения о ролях участников Azure обозреватель данных см. в разделе [авторизация на основе ролей](kusto/management/access-control/role-based-authorization.md). Сведения об управлении ролями безопасности см. в разделе [Управление ролями безопасности](kusto/management/security-roles.md).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Приемник Spark: запись в Azure обозреватель данных

1. Настройка параметров приемника:

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

1. Запись данных Spark в кластер Azure обозреватель данных в качестве пакета:

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_APP_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_APP_SECRET, appKey)
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

## <a name="spark-source-reading-from-azure-data-explorer"></a>Источник Spark: чтение из обозреватель данных Azure

1. При чтении [небольших объемов данных](kusto/concepts/querylimits.md)Определите запрос данных:

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_APP_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_APP_SECRET -> appKey
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

1. Необязательно. Если **вы** предоставляете временное хранилище BLOB-объектов (а не Azure обозреватель данных), большие двоичные объекты создаются под обязанностью вызывающего. Сюда входит подготовка хранилища, вращение ключей доступа и удаление временных артефактов. 
    Модуль Кустоблобсторажеутилс содержит вспомогательные функции для удаления больших двоичных объектов на основе координат учетной записи и контейнера и учетных данных учетной записи либо полного URL-адреса SAS с разрешениями на запись, чтение и перечисление. Если соответствующий RDD больше не нужен, каждая транзакция хранит временные артефакты больших двоичных объектов в отдельном каталоге. Этот каталог записывается как часть журналов сведений о транзакциях чтения и записи, о которых сообщается на узле драйвера Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    В приведенном выше примере доступ к Key Vault не осуществляется с помощью интерфейса соединителя. используется более простой метод использования секретов блоков данных.

1. Чтение из обозреватель данных Azure.

    * Если **вы** предоставляете временное хранилище BLOB-объектов, выполните чтение из обозреватель данных Azure следующим образом:

        ```scala
         val conf3 = Map(
              KustoSourceOptions.KUSTO_AAD_APP_ID -> appId,
              KustoSourceOptions.KUSTO_AAD_APP_SECRET -> appKey
              KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
        val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
        
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

    * Если **azure обозреватель данных** предоставляет временное хранилище BLOB-объектов, выполните чтение из обозреватель данных Azure следующим образом:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>Дальнейшие шаги

* Дополнительные сведения о [соединителе Azure обозреватель данных Spark](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Пример кода для Java и Python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
