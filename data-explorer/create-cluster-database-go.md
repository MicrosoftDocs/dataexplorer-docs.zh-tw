---
title: 使用 Azure Go SDK 建立 Azure 資料總管叢集 & 資料庫
description: 瞭解如何使用 Azure Go SDK 來建立、列出及刪除 Azure 資料總管叢集和資料庫。
author: orspod
ms.author: orspodek
ms.reviewer: abhishgu
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 833a801e6455fd4d88fbbbab83010aea1d406f02
ms.sourcegitcommit: 7edce9d9d20f9c0505abda67bb8cc3d2ecd60d15
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96524244"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-using-go"></a>使用 Go 建立 Azure 資料總管叢集和資料庫

> [!div class="op_single_selector"]
> * [入口網站](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Go](create-cluster-database-go.md)
> * [ARM 範本](create-cluster-database-resource-manager.md)

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料流。 若要使用 Azure 資料總管，請先建立叢集，然後在該叢集中建立一或多個資料庫。 然後將資料內嵌 (載入) 至資料庫，讓您可以對資料執行查詢。 

在本文中，您會使用 [Go](https://golang.org/)建立 Azure 資料總管叢集和資料庫。 然後，您可以列出並刪除您的新叢集和資料庫，並在您的資源上執行作業。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free)。
* 安裝 [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)。
* 安裝適當版本的 Go。 如需有關支援版本的詳細資訊，請參閱 [Azure GO SDK](https://github.com/Azure/azure-sdk-for-go)。

## <a name="review-the-code"></a>檢閱程式碼

此為選擇性區段。 如果您想要瞭解程式碼的運作方式，您可以參閱下列程式碼片段。 或是，您可以直接跳到[執行應用程式](#run-the-application)。

### <a name="authentication"></a>驗證

在執行任何作業之前，程式必須先向 Azure 資料總管進行驗證。 [驗證使用用戶端認證驗證類型](/azure/developer/go/azure-sdk-authorization#use-environment-based-authentication) [。NewAuthorizerFromEnvironment](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromEnvironment) ，它會尋找下列預先定義的環境變數： `AZURE_CLIENT_ID` 、 `AZURE_CLIENT_SECRET` 、 `AZURE_TENANT_ID` 。

下列範例顯示 [kusto。ClustersClient](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v48.2.0+incompatible/services/kusto/mgmt/2020-02-15/kusto) 會使用這項技術來建立：

```go
func getClustersClient(subscription string) kusto.ClustersClient {
    client := kusto.NewClustersClient(subscription)
    authR, err := auth.NewAuthorizerFromEnvironment()
    if err != nil {
        log.Fatal(err)
    }
    client.Authorizer = authR

    return client
}
```

> [!TIP]
> 使用 [驗證。](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromCLIWithResource) 如果您已安裝 Azure CLI 並設定以進行驗證，則為本機開發的 NewAuthorizerFromCLIWithResource 函式。

### <a name="create-cluster"></a>建立叢集

使用上的 [CreateOrUpdate](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto) 函式 `kusto.ClustersClient` 來建立新的 Azure 資料總管叢集。 等候進程完成，再檢查結果。

```go
func createCluster(sub, name, location, rgName string) {
    ...
    result, err := client.CreateOrUpdate(ctx, rgName, name, kusto.Cluster{Location: &location, Sku: &kusto.AzureSku{Name: kusto.DevNoSLAStandardD11V2, Capacity: &numInstances, Tier: kusto.Basic}})
    ...
    err = result.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := result.Result(client)
}
```

### <a name="list-clusters"></a>列出叢集

使用上的 [ListByResourceGroup](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#ClustersClient.ListByResourceGroup) 函數 `kusto.ClustersClient` 取得 [kusto。](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#ClusterListResult) 然後，ClusterListResult 會逐一查看以表格格式顯示輸出。


```go
func listClusters(sub, rgName string) {
    ...
    result, err := getClustersClient(sub).ListByResourceGroup(ctx, rgName)
    ...
    for _, c := range *result.Value {
        // setup tabular representation
    }
    ...
}
```

### <a name="create-database"></a>建立資料庫

在 kusto 上使用 [CreateOrUpdate](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabasesClient.CreateOrUpdate) 函數 [。DatabasesClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabasesClient) 在現有的叢集中建立新的 Azure 資料總管資料庫。 等候進程完成，再檢查結果。


```go
func createDatabase(sub, rgName, clusterName, location, dbName string) {
    future, err := client.CreateOrUpdate(ctx, rgName, clusterName, dbName, kusto.ReadWriteDatabase{Kind: kusto.KindReadWrite, Location: &location})
    ...
    err = future.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := future.Result(client)
    ...
}
```

### <a name="list-databases"></a>列出資料庫

使用 [ListByCluster](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabasesClient.ListByCluster) 函數 `kusto.DatabasesClient` 來取得 [kusto。](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabaseListResult) 然後，DatabaseListResult 會逐一查看以表格格式顯示輸出。


```go
func listDatabases(sub, rgName, clusterName string) {
    result, err := getDBClient(sub).ListByCluster(ctx, rgName, clusterName)
    ...
    for _, db := range *result.Value {
        // setup tabular representation
    }
    ...
}
```

### <a name="delete-database"></a>刪除資料庫

在上使用 [delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabasesClient.Delete) 函數 `kusto.DatabasesClient` 可刪除叢集中現有的資料庫。 等候進程完成，再檢查結果。

```go
func deleteDatabase(sub, rgName, clusterName, dbName string) {
    ...
    future, err := getDBClient(sub).Delete(ctx, rgName, clusterName, dbName)
    ...
    err = future.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := future.Result(client)
    if r.StatusCode == 200 {
        // determine success or failure
    }
    ...
}
```

### <a name="delete-cluster"></a>刪除叢集

在上使用 [delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#ClustersClient.Delete) 函數 `kusto.ClustersClient` 可刪除叢集。 等候進程完成，再檢查結果。

```go
func deleteCluster(sub, clusterName, rgName string) {
    result, err := client.Delete(ctx, rgName, clusterName)
    ...
    err = result.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := result.Result(client)
    if r.StatusCode == 200 {
        // determine success or failure
    }
    ...
}
```

## <a name="run-the-application"></a>執行應用程式

當您依原樣執行範例程式碼時，會執行下列動作：
    
1. 建立 Azure 資料總管叢集。
1. 列出指定資源群組中的所有 Azure 資料總管叢集。
1. 在現有的叢集中建立 Azure 資料總管資料庫。
1. 列出指定叢集中的所有資料庫。
1. 資料庫已刪除。
1. 已刪除叢集。


    ```go
    func main() {
        createCluster(subscription, clusterNamePrefix+clusterName, location, rgName)
        listClusters(subscription, rgName)
        createDatabase(subscription, rgName, clusterNamePrefix+clusterName, location, dbNamePrefix+databaseName)
        listDatabases(subscription, rgName, clusterNamePrefix+clusterName)
        deleteDatabase(subscription, rgName, clusterNamePrefix+clusterName, dbNamePrefix+databaseName)
        deleteCluster(subscription, clusterNamePrefix+clusterName, rgName)
    }
    ```

    > [!TIP]
    > 若要嘗試不同的作業組合，您可以視需要取消批註和批註各自的函式 `main.go` 。

1. 從 GitHub 複製範例程式碼：

    ```console
    git clone https://github.com/Azure-Samples/azure-data-explorer-go-cluster-management.git
    cd azure-data-explorer-go-cluster-management
    ```

1. 程式會使用用戶端認證進行驗證。 使用 Azure CLI [az ad sp 建立-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令來建立服務主體。 儲存用戶端識別碼、用戶端密碼和租使用者識別碼資訊，以便在下一個步驟中使用。

1. 匯出必要的環境變數，包括服務主體資訊。 輸入您要在其中建立叢集的訂用帳戶識別碼、資源群組和區域。

    ```console
    export AZURE_CLIENT_ID="<enter service principal client ID>"
    export AZURE_CLIENT_SECRET="<enter service principal client secret>"
    export AZURE_TENANT_ID="<enter tenant ID>"

    export SUBSCRIPTION="<enter subscription ID>"
    export RESOURCE_GROUP="<enter resource group name>"
    export LOCATION="<enter azure location e.g. Southeast Asia>"

    export CLUSTER_NAME_PREFIX="<enter prefix (cluster name will be [prefix]-ADXTestCluster)>"
    export DATABASE_NAME_PREFIX="<enter prefix (database name will be [prefix]-ADXTestDB)>"
    ```
    
    > [!TIP]
    > 您可能會在生產案例中使用環境變數，以提供認證給您的應用程式。 如先前所述，在本機開發的程式 [代碼](#review-the-code)中，請使用 [auth。](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromCLIWithResource) 如果您已安裝 Azure CLI 並設定進行驗證，請 NewAuthorizerFromCLIWithResource。 在這種情況下，您不需要建立服務主體。


1. 執行程式：

    ```console
    go run main.go
    ```

    您將會得到類似下列的輸出：

    ```console
    waiting for cluster creation to complete - fooADXTestCluster
    created cluster fooADXTestCluster
    listing clusters in resource group <your resource group>
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    |       NAME        |  STATE  |    LOCATION    | INSTANCES |                            URI                           |
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    | fooADXTestCluster | Running | Southeast Asia |         1 | https://fooADXTestCluster.southeastasia.kusto.windows.net |
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    
    waiting for database creation to complete - barADXTestDB
    created DB fooADXTestCluster/barADXTestDB with ID /subscriptions/<your subscription ID>/resourceGroups/<your resource group>/providers/Microsoft.Kusto/Clusters/fooADXTestCluster/Databases/barADXTestDB and type Microsoft.Kusto/Clusters/Databases
    
    listing databases in cluster fooADXTestCluster
    +--------------------------------+-----------+----------------+------------------------------------+
    |              NAME              |   STATE   |    LOCATION    |                TYPE                |
    +--------------------------------+-----------+----------------+------------------------------------+
    | fooADXTestCluster/barADXTestDB | Succeeded | Southeast Asia | Microsoft.Kusto/Clusters/Databases |
    +--------------------------------+-----------+----------------+------------------------------------+
    
    waiting for database deletion to complete - barADXTestDB
    deleted DB barADXTestDB from cluster fooADXTestCluster

    waiting for cluster deletion to complete - fooADXTestCluster
    deleted ADX cluster fooADXTestCluster from resource group <your resource group>
    ```

## <a name="clean-up-resources"></a>清除資源

如果您未使用本文中的範例程式碼以程式設計方式刪除叢集，請使用 [Azure CLI](create-cluster-database-cli.md#clean-up-resources)手動將其刪除。

## <a name="next-steps"></a>後續步驟

[使用 Azure 資料總管 Go SDK 內嵌資料](go-ingest-data.md)
