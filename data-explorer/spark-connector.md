---
title: 使用適用于 Apache Spark 的 Azure 資料總管連接器，在 Azure 資料總管和 Spark 叢集之間移動資料。
description: 本主題說明如何在 Azure 資料總管和 Apache Spark 叢集之間移動資料。
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: 28dee67b6ac412a9c0497d5713a69c9617d3ae55
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370474"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>適用于 Apache Spark 的 Azure 資料總管連接器

[Apache Spark](https://spark.apache.org/)是適用于大規模資料處理的整合分析引擎。 Azure 資料總管是快速、完全受控的資料分析服務，可即時分析大量資料。 

適用于 Spark 的 Azure 資料總管連接器是可在任何 Spark 叢集上執行的[開放原始碼專案](https://github.com/Azure/azure-kusto-spark)。 它會執行資料來源和資料接收，以便在 Azure 資料總管和 Spark 叢集之間移動資料。 您可以使用 Azure 資料總管和 Apache Spark，建立以資料導向案例為目標的快速且可擴充的應用程式。 例如，機器學習（ML）、解壓縮-轉換-載入（ETL）和 Log Analytics。 使用連接器時，Azure 資料總管會成為標準 Spark 來源和接收作業的有效資料存放區，例如 write、read 和 Writestream.format。

您可以在批次或串流模式中寫入 Azure 資料總管。 從 Azure 資料總管讀取可支援資料行剪除和述詞下推，這會篩選 Azure 資料總管中的資料，以減少傳送的資料量。

本主題說明如何安裝和設定 Azure 資料總管 Spark 連接器，並在 Azure 資料總管與 Apache Spark 叢集之間移動資料。

> [!NOTE]
> 雖然以下的部分範例參考[Azure Databricks](https://docs.azuredatabricks.net/) Spark 叢集，但 Azure 資料總管 Spark 連接器並不會直接相依于 Databricks 或任何其他 spark 散發。

## <a name="prerequisites"></a>Prerequisites

* [建立 Azure 資料總管叢集和資料庫](create-cluster-database-portal.md) 
* 建立 Spark 叢集
* 安裝 Azure 資料總管連接器程式庫：
    * [Spark 2.4、Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases)的預先建立程式庫 
    * [Maven 存放庫](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* 已安裝[Maven](https://maven.apache.org/download.cgi) 3。x

> [!TIP]
> 2.3. x 版本也受到支援，但可能需要在 pom .xml 相依性中進行一些變更。

## <a name="how-to-build-the-spark-connector"></a>如何建立 Spark 連接器

> [!NOTE]
> 此為選用步驟。 如果您使用的是預先建立的程式庫，請移至[Spark 叢集設定](#spark-cluster-setup)。

### <a name="build-prerequisites"></a>組建必要條件

1. 安裝 [相依性[]](https://github.com/Azure/azure-kusto-spark#dependencies)中列出的程式庫，包括下列[Kusto JAVA SDK](kusto/api/java/kusto-java-client-library.md)程式庫：
    * [Kusto 資料用戶端](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto 內嵌用戶端](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. 請參閱[此來源](https://github.com/Azure/azure-kusto-spark)以建立 Spark 連接器。

1. 針對使用 Maven 專案定義的 Scala/JAVA 應用程式，請將您的應用程式與下列成品連結（最新版本可能不同）：
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>建置命令

若要建置 jar 並執行所有測試：

```
mvn clean package
```

若要建立 jar、執行所有測試，並將 jar 安裝到您的本機 Maven 存放庫：

```
mvn clean install
```

如需詳細資訊，請參閱[連接器使用](https://github.com/Azure/azure-kusto-spark#usage)方式。

## <a name="spark-cluster-setup"></a>Spark 叢集設定

> [!NOTE]
> 建議您在執行下列步驟時使用最新的 Azure 資料總管 Spark 連接器版本。

1. 使用 Spark 2.4.4 和 Scala 2.11，根據 Azure Databricks 叢集來設定下列 Spark 叢集設定：

    ![Databricks 叢集設定](media/spark-connector/databricks-cluster.png)
    
1. 從 Maven 安裝最新的 spark kusto 連接器程式庫：
    
    ![匯入程式庫 ](media/spark-connector/db-libraries-view.png) ![ 選取 Spark-Kusto-連接器](media/spark-connector/db-dependencies.png)

1. 確認已安裝所有必要的程式庫：

    ![確認已安裝程式庫](media/spark-connector/db-libraries-view.png)

1. 若要使用 JAR 檔案進行安裝，請確認已安裝其他相依性：

    ![新增相依性](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>驗證

Azure 資料總管 Spark 連接器可讓您使用下列其中一種方法，透過 Azure Active Directory （Azure AD）進行驗證：
* [Azure AD 應用程式](#azure-ad-application-authentication)
* [Azure AD 存取權杖](https://github.com/Azure/azure-kusto-spark/blob/master/docs/Authentication.md#direct-authentication-with-access-token)
* [裝置驗證](https://github.com/Azure/azure-kusto-spark/blob/master/docs/Authentication.md#device-authentication)（適用于非生產案例）
* 存取 Key Vault 資源的[Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/master/docs/Authentication.md#key-vault) ，安裝 keyvault 套件並提供應用程式認證。

### <a name="azure-ad-application-authentication"></a>Azure AD 應用程式驗證

Azure AD 應用程式驗證是最簡單且最常見的驗證方法，建議用於 Azure 資料總管 Spark 連接器。

|屬性  |描述  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD 應用程式（用戶端）識別碼。      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD 的驗證授權單位。 Azure AD Directory （租使用者）識別碼。        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Azure AD 用戶端的應用程式金鑰。     |

### <a name="azure-data-explorer-privileges"></a>Azure 資料總管許可權

在 Azure 資料總管叢集上授與下列許可權：

* 若要讀取（資料來源），Azure AD 身分識別必須具有目標資料庫的*檢視器*許可權，或目標資料表上的系統*管理員*許可權。
* 對於寫入（資料接收），Azure AD 身分識別必須具有目標資料庫的*擷取器*許可權。 它也必須擁有目標資料庫的*使用者*權力，才能建立新的資料表。 如果目標資料表已經存在，您就必須在目標資料表上設定系統*管理員*許可權。
 
如需 Azure 資料總管主體角色的詳細資訊，請參閱以[角色為基礎的授權](kusto/management/access-control/role-based-authorization.md)。 如需管理安全性角色，請參閱[安全性角色管理](kusto/management/security-roles.md)。

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark 接收：寫入 Azure 資料總管

1. 設定接收參數：

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

1. 將 Spark 資料框架寫入 Azure 資料總管叢集作為批次：

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
    
   或使用簡化的語法：
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. 寫入串流資料：

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

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark 來源：從 Azure 資料總管讀取

1. 讀取[少量資料](kusto/concepts/querylimits.md)時，請定義資料查詢：

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

1. 選擇性：如果**您**提供暫時性 blob 儲存體（而非 Azure 資料總管），則會在呼叫者的責任下建立 blob。 這包括布建儲存體、輪替存取金鑰，以及刪除暫時性成品。 
    KustoBlobStorageUtils 模組包含 helper 函式，可用於根據帳戶和容器座標和帳號憑證來刪除 blob，或使用具有寫入、讀取和列出許可權的完整 SAS URL。 當不再需要對應的 RDD 時，每個交易都會在不同的目錄中儲存暫時性的 blob 成品。 此目錄會在 Spark 驅動程式節點上所報告的讀取事務資訊記錄中捕捉。

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    在上述範例中，不會使用連接器介面來存取 Key Vault。使用 Databricks 秘密的較簡單方法。

1. 從 Azure 資料總管讀取。

    * 如果**您**提供暫時性 blob 儲存體，請從 Azure 資料總管讀取，如下所示：

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

    * 如果**azure 資料總管**提供暫時性 blob 儲存體，請從 azure 資料總管讀取，如下所示：
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Azure 資料總管 Spark 連接器](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [JAVA 和 Python 的範例程式碼](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
