---
title: 使用 Azure 資料總管 Go SDK 內嵌資料
description: 在本文中，您將瞭解如何使用 Go SDK，將) 資料的 (載入至 Azure 資料總管。
author: abhirockzz
ms.author: abhishgu
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 38063f3e00ebd22da17d48abba1dd9b3510273ec
ms.sourcegitcommit: bcd0c96b1581e43e33aa35f4d68af6dcb4979d39
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88039726"
---
# <a name="ingest-data-using-the-azure-data-explorer-go-sdk"></a>使用 Azure 資料總管 Go SDK 內嵌資料 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [節點](node-ingest-data.md)
> * [Go](go-ingest-data.md)

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 它提供可與 Azure 資料總管服務互動的[GO SDK 用戶端程式庫](kusto/api/golang/kusto-golang-client-library.md)。 您可以使用[GO SDK](https://github.com/Azure/azure-kusto-go)來內嵌、控制及查詢 Azure 資料總管叢集中的資料。 

在本文中，您會先在測試叢集中建立資料表和資料對應。 接著，您可以使用 Go SDK 將內嵌加入叢集，並驗證結果。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 安裝 [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。
* 使用下列[GO SDK 的最低需求](kusto/api/golang/kusto-golang-client-library.md#minimum-requirements)來安裝[Go](https://golang.org/) 。 
* 建立[Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)。

## <a name="install-the-go-sdk"></a>安裝 Go SDK

當您執行 [使用[Go 模組](https://golang.org/ref/mod)的範例應用程式] 時，將會自動安裝 Azure 資料總管 Go SDK。 如果您已安裝適用于另一個應用程式的 Go SDK，請建立 Go 模組，並使用) 提取 Azure 資料總管套件 (例如 `go get` ：

```console
go mod init foo.com/bar
go get github.com/Azure/azure-kusto-go/kusto
```

封裝相依性將會新增至檔案 `go.mod` 。 在 Go 應用程式中使用它。

## <a name="review-the-code"></a>檢閱程式碼

這段[檢查程式碼](#review-the-code)區段是選擇性的。 如果您想要瞭解程式碼的運作方式，您可以查看下列程式碼片段。 或是，您可以直接跳到[執行應用程式](#run-the-application)。

### <a name="authenticate"></a>Authenticate

在執行任何作業之前，程式必須向 Azure 資料總管服務進行驗證。

```go
auth := kusto.Authorization{Config: auth.NewClientCredentialsConfig(clientID, clientSecret, tenantID)}
client, err := kusto.New(kustoEndpoint, auth)
```

Kusto 的實例[。授權](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Authorization)是使用服務主體認證所建立。 然後，它會用來建立[kusto。](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Client)具有[新](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#New])函式的用戶端也會接受叢集端點。

### <a name="create-table"></a>建立資料表

Create table 命令是以[Kusto 語句](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Stmt)表示。管理[函數是](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Client.Mgmt)用來執行管理命令。 它是用來執行命令以建立資料表。 

```go
func createTable(kc *kusto.Client, kustoDB string) {
    _, err := kc.Mgmt(context.Background(), kustoDB, kusto.NewStmt(createTableCommand))
    if err != nil {
        log.Fatal("failed to create table", err)
    }
    log.Printf("Table %s created in DB %s\n", kustoTable, kustoDB)
}
```

> [!TIP]
> 根據預設，Kusto 語句是常數，以獲得更好的安全性。 [`NewStmt`](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#NewStmt)接受字串常數。 [`UnsafeStmt`](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#UnsafeStmt)API 允許使用非常數語句區段，但不建議這麼做。

Kusto create table 命令如下所示：

```kusto
.create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
```

### <a name="create-mapping"></a>建立對應

資料對應會在內嵌期間用來將傳入的資料對應至 Azure 資料總管資料表中的資料行。 如需詳細資訊，請參閱[資料對應](kusto/management/mappings.md)。 對應的建立方式與資料表相同，使用 `Mgmt` 函數搭配資料庫名稱和適當的命令。 [範例的 GitHub](https://github.com/Azure-Samples/Azure-Data-Explorer-Go-SDK-example-to-ingest-data/blob/main/main.go#L20)存放庫中提供完整的命令。

```go
func createMapping(kc *kusto.Client, kustoDB string) {
    _, err := kc.Mgmt(context.Background(), kustoDB, kusto.NewStmt(createMappingCommand))
    if err != nil {
        log.Fatal("failed to create mapping - ", err)
    }
    log.Printf("Mapping %s created\n", kustoMappingRefName)
}
```

### <a name="ingest-data"></a>擷取資料

內嵌會使用現有 Azure Blob 儲存體容器中的檔案來排入佇列。 

```go
func ingestFile(kc *kusto.Client, blobStoreAccountName, blobStoreContainer, blobStoreToken, blobStoreFileName, kustoMappingRefName, kustoDB, kustoTable string) {
    kIngest, err := ingest.New(kc, kustoDB, kustoTable)
    if err != nil {
        log.Fatal("failed to create ingestion client", err)
    }
    blobStorePath := fmt.Sprintf(blobStorePathFormat, blobStoreAccountName, blobStoreContainer, blobStoreFileName, blobStoreToken)
    err = kIngest.FromFile(context.Background(), blobStorePath, ingest.FileFormat(ingest.CSV), ingest.IngestionMappingRef(kustoMappingRefName, ingest.CSV))

    if err != nil {
        log.Fatal("failed to ingest file", err)
    }
    log.Println("Ingested file from -", blobStorePath)
}
```

內嵌[用戶端](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#Ingestion)是使用內嵌建立的[。新增](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#New)。 [FromFile](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#Ingestion.FromFile)函數是用來參考 Azure Blob 儲存體 URI。 對應參考名稱和資料類型會以[FileOption](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#FileOption)的形式傳遞。 

## <a name="run-the-application"></a>執行應用程式

1. 從 GitHub 複製範例程式碼：

    ```console
    git clone https://github.com/Azure-Samples/Azure-Data-Explorer-Go-SDK-example-to-ingest-data.git
    cd Azure-Data-Explorer-Go-SDK-example-to-ingest-data
    ```

1. 從執行此程式碼片段中所示的範例程式碼 `main.go` ： 

    ```go
    func main {
        ...
        dropTable(kc, kustoDB)
        createTable(kc, kustoDB)
        createMapping(kc, kustoDB)
        ingestFile(kc, blobStoreAccountName, blobStoreContainer, blobStoreToken, blobStoreFileName, kustoMappingRefName, kustoDB, kustoTable)
        ...
    }
    ```

    > [!TIP]
    > 若要嘗試不同的作業組合，您可以將中的個別函式取消批註/批註 `main.go` 。

    當您執行範例程式碼時，會執行下列動作：
    
    1. **Drop table**： `StormEvents` 如果資料表存在) 中，則會卸載它 (。
    1. **資料表建立**： `StormEvents` 資料表已建立。
    1. 建立**對應**： `StormEvents_CSV_Mapping` 建立對應。
    1. 檔案**內嵌： Azure Blob 儲存體**) 中 (的 CSV 檔案已排入佇列以進行內嵌。

1. 若要建立驗證的服務主體，請使用 Azure CLI 搭配[az ad sp create-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)命令。 以程式將使用的環境變數形式，將服務主體資訊設定為叢集端點和資料庫名稱：

    ```console
    export AZURE_SP_CLIENT_ID="<replace with appID>"
    export AZURE_SP_CLIENT_SECRET="<replace with password>"
    export AZURE_SP_TENANT_ID="<replace with tenant>"
    export KUSTO_ENDPOINT="https://<cluster name>.<azure region>.kusto.windows.net"
    export KUSTO_DB="name of the database"
    ```

1. 執行程式：

    ```console
    go run main.go
    ```

    您會得到類似的輸出：

    ```console
    Connected to Azure Data Explorer
    Using database - testkustodb
    Failed to drop StormEvents table. Maybe it does not exist?
    Table StormEvents created in DB testkustodb
    Mapping StormEvents_CSV_Mapping created
    Ingested file from - https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D
    ```

## <a name="validate-and-troubleshoot"></a>驗證和疑難排解

等待佇列內嵌的5至10分鐘，以排程內嵌進程，並將資料載入 Azure 資料總管。 

1. 登入 [https://dataexplorer.azure.com](https://dataexplorer.azure.com) 並聯機到您的叢集。 然後執行下列命令，以取得資料表中的記錄計數 `StormEvents` 。

    ```kusto
    StormEvents | count
    ```

2. 在資料庫中執行下列命令，以查看最後四個小時是否有任何擷取失敗。 先取代資料庫名稱，再執行。

    ```kusto
    .show ingestion failures
    | where FailedOn > ago(4h) and Database == "<DatabaseName>"
    ```

1. 執行下列命令，以檢視最後四個小時內的所有擷取作業狀態。 先取代資料庫名稱，再執行。

    ```kusto
    .show operations
    | where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
    | summarize arg_max(LastUpdatedOn, *) by OperationId
    ```

## <a name="clean-up-resources"></a>清除資源

如果您打算遵循其他文章，請保留您建立的資源。 如果不是，請在您的資料庫中執行下列命令，以卸載 `StormEvents` 資料表。

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>後續步驟

[撰寫查詢](write-queries.md)
