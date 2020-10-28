---
title: 使用 Azure 資料總管 JAVA SDK 內嵌資料
description: 在本文中，您將瞭解如何使用 JAVA SDK，將) 資料內嵌 (載入至 Azure 資料總管。
author: orspod
ms.author: orspodek
ms.reviewer: abhishgu
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: 45b0ce7b1716a4ed2ab7277b6c99a5d95f8ad5af
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92906258"
---
# <a name="ingest-data-using-the-azure-data-explorer-java-sdk"></a>使用 Azure 資料總管 JAVA SDK 內嵌資料 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [節點](node-ingest-data.md)
> * [Go](go-ingest-data.md)
> * [Java](java-ingest-data.md)

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 [JAVA 用戶端程式庫](kusto/api/java/kusto-java-client-library.md)可以用來內嵌資料、發出控制命令，以及查詢 Azure 資料總管叢集中的資料。

在本文中，瞭解如何使用 Azure 資料總管 JAVA 程式庫內嵌資料。 首先，您將在測試叢集中建立資料表和資料對應。 然後，您會使用 JAVA SDK 將內嵌從 blob 儲存體佇列到叢集，並驗證結果。

## <a name="prerequisites"></a>Prerequisites

* [免費的 Azure 帳戶](https://azure.microsoft.com/free/)。
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。
* JDK 1.8 版或更新版本。
* [Maven](https://maven.apache.org/download.cgi).
* 叢集 [和資料庫](create-cluster-database-portal.md)。
* 建立 [應用程式註冊，並授與資料庫的許可權](provision-azure-ad-app.md)。 儲存用戶端識別碼和用戶端密碼以供稍後使用。

## <a name="review-the-code"></a>檢閱程式碼

此為選擇性區段。 請參閱下列程式碼片段，以瞭解程式碼的運作方式。 若要略過此區段，請移至 [[執行應用程式](#run-the-application)]。

### <a name="authentication"></a>驗證

此程式會使用 Azure Active Directory 的驗證認證搭配 ConnectionStringBuilder '。

1. 建立 `com.microsoft.azure.kusto.data.Client` 查詢和管理的。

    ```java
    static Client getClient() throws Exception {
        ConnectionStringBuilder csb = ConnectionStringBuilder.createWithAadApplicationCredentials(endpoint, clientID, clientSecret, tenantID);
        return ClientFactory.createClient(csb);
    }
    ```

1. 建立並使用將 `com.microsoft.azure.kusto.ingest.IngestClient` 資料內嵌到 Azure 資料總管中的佇列：

    ```java
    static IngestClient getIngestionClient() throws Exception {
        String ingestionEndpoint = "https://ingest-" + URI.create(endpoint).getHost();
        ConnectionStringBuilder csb = ConnectionStringBuilder.createWithAadApplicationCredentials(ingestionEndpoint, clientID, clientSecret);
        return IngestClientFactory.createClient(csb);
    }
    ```

### <a name="management-commands"></a>管理命令

[控制命令](kusto/management/commands.md)（例如 [`.drop`](kusto/management/drop-function.md) 和 [`.create`](kusto/management/create-function.md) ）是 `execute` 在物件上呼叫來執行 `com.microsoft.azure.kusto.data.Client` 。

例如，資料表的 `StormEvents` 建立方式如下：

```java
static final String createTableCommand = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)";

static void createTable(String database) {
    try {
        getClient().execute(database, createTableCommand);
        System.out.println("Table created");
    } catch (Exception e) {
        System.out.println("Failed to create table: " + e.getMessage());
        return;
    }
    
}
```

### <a name="data-ingestion"></a>資料擷取

使用現有 Azure Blob 儲存體容器中的檔案來內嵌佇列。 
* 使用 `BlobSourceInfo` 指定 Blob 儲存體路徑。
* 用 `IngestionProperties` 來定義資料表、資料庫、對應名稱和資料類型。 在下列範例中，資料類型為 `CSV` 。

```java
    ...
    static final String blobPathFormat = "https://%s.blob.core.windows.net/%s/%s%s";
    static final String blobStorageAccountName = "kustosamplefiles";
    static final String blobStorageContainer = "samplefiles";
    static final String fileName = "StormEvents.csv";
    static final String blobStorageToken = "??st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    ....

    static void ingestFile(String database) throws InterruptedException {
        String blobPath = String.format(blobPathFormat, blobStorageAccountName, blobStorageContainer,
                fileName, blobStorageToken);
        BlobSourceInfo blobSourceInfo = new BlobSourceInfo(blobPath);

        IngestionProperties ingestionProperties = new IngestionProperties(database, tableName);
        ingestionProperties.setDataFormat(DATA_FORMAT.csv);
        ingestionProperties.setIngestionMapping(ingestionMappingRefName, IngestionMappingKind.Csv);
        ingestionProperties.setReportLevel(IngestionReportLevel.FailuresAndSuccesses);
        ingestionProperties.setReportMethod(IngestionReportMethod.QueueAndTable);
    ....
```

內嵌進程會在個別的執行緒中啟動， `main` 執行緒會等待內嵌執行緒完成。 此進程會使用 [CountdownLatch](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CountDownLatch.html)。 內嵌 API (`IngestClient#ingestFromBlob`) 不是非同步。 `while`迴圈可用來每隔5秒輪詢一次目前的狀態，並等候內嵌狀態從變更 `Pending` 為不同的狀態。 最終狀態可以是 `Succeeded` 、 `Failed` 或 `PartiallySucceeded` 。

```java
        ....
        CountDownLatch ingestionLatch = new CountDownLatch(1);
        new Thread(new Runnable() {
            @Override
            public void run() {
                IngestionResult result = null;
                try {
                    result = getIngestionClient().ingestFromBlob(blobSourceInfo, ingestionProperties);
                } catch (Exception e) {
                    ingestionLatch.countDown();
                }
                try {
                    IngestionStatus status = result.getIngestionStatusCollection().get(0);
                    while (status.status == OperationStatus.Pending) {
                        Thread.sleep(5000);
                        status = result.getIngestionStatusCollection().get(0);
                    }
                    ingestionLatch.countDown();
                } catch (Exception e) {
                    ingestionLatch.countDown();
                }
            }
        }).start();
        ingestionLatch.await();
    }
```

> [!TIP]
> 有其他方法可以針對不同的應用程式以非同步方式處理內嵌。 例如，您可以使用 [`CompletableFuture`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) 建立管線來定義內嵌動作（例如查詢資料表），或處理回報給的例外狀況 `IngestionStatus` 。

## <a name="run-the-application"></a>執行應用程式

### <a name="general"></a>一般

當您執行範例程式碼時，會執行下列動作：

   1. **Drop table** ： `StormEvents` 當資料表存在) 時，會卸載資料表 (。
   1. **資料表建立** ： `StormEvents` 已建立資料表。
   1. 建立 **對應** ： `StormEvents_CSV_Mapping` 建立對應。
   1. 檔案 **內嵌： Azure Blob 儲存體** ) 中 (的 CSV 檔案已排入佇列以進行內嵌。

下列範例程式碼來自 `App.java` ：

```java
public static void main(final String[] args) throws Exception {
    dropTable(database);
    createTable(database);
    createMapping(database);
    ingestFile(database);
}
```

> [!TIP]
> 若要嘗試不同的作業組合，請取消批註/批註中的個別方法 `App.java` 。

### <a name="run-the-application"></a>執行應用程式

1. 從 GitHub 複製範例程式碼：

    ```console
    git clone https://github.com/Azure-Samples/azure-data-explorer-java-sdk-ingest.git
    cd azure-data-explorer-java-sdk-ingest
    ```

1. 使用下列資訊，將服務主體資訊設定為程式所使用的環境變數：
    * 叢集端點 
    * 資料庫名稱 

    ```console
    export AZURE_SP_CLIENT_ID="<replace with appID>"
    export AZURE_SP_CLIENT_SECRET="<replace with password>"
    export KUSTO_ENDPOINT="https://<cluster name>.<azure region>.kusto.windows.net"
    export KUSTO_DB="name of the database"
    ```

1. 組建並執行：

    ```console
    mvn clean package
    java -jar target/adx-java-ingest-jar-with-dependencies.jar
    ```

    輸出將類似於：

    ```console
    Table dropped
    Table created
    Mapping created
    Waiting for ingestion to complete...
    ```

請等候幾分鐘，讓內嵌進程完成。 成功完成之後，您會看到下列記錄訊息： `Ingestion completed successfully` 。 您可以在此時結束程式並移至下一個步驟，而不會影響已排入佇列的內嵌進程。

## <a name="validate"></a>Validate

等候五到10分鐘，讓佇列的內嵌排程內嵌進程，並將資料載入 Azure 資料總管。 

1. 登入 [https://dataexplorer.azure.com](https://dataexplorer.azure.com) 並聯機至您的叢集。 
1. 執行下列命令以取得資料表中的記錄計數 `StormEvents` ：
    
    ```kusto
    StormEvents | count
    ```

## <a name="troubleshoot"></a>疑難排解

1. 若要查看過去四小時內的內嵌失敗，請在您的資料庫上執行下列命令： 

    ```kusto
    .show ingestion failures
    | where FailedOn > ago(4h) and Database == "<DatabaseName>"
    ```

1. 若要在過去四小時內查看所有內嵌作業的狀態，請執行下列命令：

    ```kusto
    .show operations
    | where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
    | summarize arg_max(LastUpdatedOn, *) by OperationId
    ```

## <a name="clean-up-resources"></a>清除資源

如果您不打算使用剛才建立的資源，請在您的資料庫中執行下列命令，以卸載 `StormEvents` 資料表。

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>後續步驟

[撰寫查詢](write-queries.md)
