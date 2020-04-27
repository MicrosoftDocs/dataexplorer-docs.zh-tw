---
title: 教學課程：在未使用程式碼的情況下在 Azure 資料總管中擷取監視資料
description: 在本教學課程中，您將了解如何將監視資料擷取至 Azure 資料總管 (完全不需使用程式碼)，然後查詢該資料。
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 59a42c2a3e4efa8c8642bccf96b0040767753e65
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108332"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>教學課程：在 Azure 資料總管中擷取和查詢監視資料 

本教學課程將教導您如何直接將診斷和活動記錄中的資料內嵌至 Azure 資料總管叢集，而不需撰寫程式碼。 透過這個簡單的擷取方法，您可以快速開始查詢 Azure 資料總管，以進行資料分析。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 在 Azure 資料總管資料庫中建立資料表和擷取對應。
> * 使用更新原則將內嵌的資料格式化。
> * 建立[事件中樞](/azure/event-hubs/event-hubs-about)並將期連線至 Azure 資料總管。
> * 將資料從 Azure 監視器[診斷計量和記錄](/azure/azure-monitor/platform/diagnostic-settings)與[活動記錄](/azure/azure-monitor/platform/activity-logs-overview)串流至事件中樞。
> * 使用 Azure 資料總管查詢內嵌的資料。

> [!NOTE]
> 在相同的 Azure 位置或區域中建立所有資源。 

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)。 在本教學課程中，資料庫名稱為 *TestDatabase*。

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Azure 監視器資料提供者：診斷計量和記錄與活動記錄

檢視並了解以下 Azure 監視器診斷計量和記錄與活動記錄所提供的資料。 您將根據這些資料結構描述建立擷取管線。 請注意，記錄 (Log) 中的每個事件都具有記錄 (Record) 陣列。 本教學課程稍後將會分割此記錄陣列。

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>診斷計量和記錄與活動記錄的範例

Azure 診斷計量和記錄與活動記錄是由 Azure 服務發出的記錄，以提供與該服務的作業有關的資料。 

# <a name="diagnostic-metrics"></a>[診斷計量](#tab/diagnostic-metrics)
#### <a name="example"></a>範例

診斷計量會以 1 分鐘的時間粒紋進行彙總。 以下是 Azure 資料總管計量事件結構描述在查詢持續時間內的範例：

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

# <a name="diagnostic-logs"></a>[診斷記錄](#tab/diagnostic-logs)
#### <a name="example"></a>範例

以下是 Azure 資料總管[診斷擷取記錄](using-diagnostic-logs.md#diagnostic-logs-schema)的範例：

```json
{
    "time": "2019-08-26T13:22:36.8804326Z",
    "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "resultType": "Failed",
    "correlationId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
    "properties": {
        "OperationId": "00000000-0000-0000-0000-000000000000",
        "Database": "Kusto",
        "Table": "Table_13_20_prod",
        "FailedOn": "2019-08-26T13:22:36.8804326Z",
        "IngestionSourceId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
        "Details":
        {
            "error": 
            {
                "code": "BadRequest_DatabaseNotExist",
                "message": "Request is invalid and cannot be executed.",
                "@type": "Kusto.Data.Exceptions.DatabaseNotFoundException",
                "@message": "Database 'Kusto' was not found.",
                "@context": 
                {
                    "timestamp": "2019-08-26T13:22:36.7179157Z",
                    "serviceAlias": "<cluster-name>",
                    "machineName": "KEngine000001",
                    "processName": "Kusto.WinSvc.Svc",
                    "processId": 5336,
                    "threadId": 6528,
                    "appDomainName": "Kusto.WinSvc.Svc.exe",
                    "clientRequestd": "DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8",
                    "activityId": "f13e7718-1153-4e65-bf82-8583d712976f",
                    "subActivityId": "2cdad9d0-737b-4c69-ac9a-22cf9af0c41b",
                    "activityType": "DN.AdminCommand.DataIngestPullCommand",
                    "parentActivityId": "2f65e533-a364-44dd-8d45-d97460fb5795",
                    "activityStack": "(Activity stack: CRID=DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8 ARID=f13e7718-1153-4e65-bf82-8583d712976f > DN.Admin.Client.ExecuteControlCommand/5b764b32-6017-44a2-89e7-860eda515d40 > P.WCF.Service.ExecuteControlCommandInternal..IAdminClientServiceCommunicationContract/c2ef9344-069d-44c4-88b1-a3570697ec77 > DN.FE.ExecuteControlCommand/2f65e533-a364-44dd-8d45-d97460fb5795 > DN.AdminCommand.DataIngestPullCommand/2cdad9d0-737b-4c69-ac9a-22cf9af0c41b)"
                },
                "@permanent": true
            }
        },
        "ErrorCode": "BadRequest_DatabaseNotExist",
        "FailureStatus": "Permanent",
        "RootActivityId": "00000000-0000-0000-0000-000000000000",
        "OriginatesFromUpdatePolicy": false,
        "ShouldRetry": false,
        "IngestionSourcePath": "https://c0skstrldkereneus01.blob.core.windows.net/aam-20190826-temp-e5c334ee145d4b43a3a2d3a96fbac1df/3216_test_3_columns_invalid_8f57f0d161ed4a8c903c6d1073005732_59951f9ca5d143b6bdefe52fa381a8ca.zip"
    }
}
```
# <a name="activity-logs"></a>[活動記錄](#tab/activity-logs)
#### <a name="example"></a>範例

Azure 活動記錄為訂用帳戶層級的記錄，可針對在訂用帳戶中資源上所執行的作業提供深入解析。 以下是用來檢查存取的活動記錄事件範例：

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```
---

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>在 Azure 資料總管中設定擷取管線

設定 Azure 資料總管管線時須執行數個步驟，例如[資料表建立和資料擷取](/azure/data-explorer/ingest-sample-data#ingest-data)。 您也可以操作、對應和更新資料。

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>連線至 Azure 資料總管 Web UI

在 Azure 資料總管的 *TestDatabase* 資料庫中選取 [查詢]  ，以開啟 Azure 資料總管 Web UI。

![查詢頁面](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>建立目標資料表

Azure 監視器記錄的結構不是表格式的。 您將操作資料，並將每個事件展開為一或多個記錄。 未經處理的資料將針對活動記錄擷取至名為 *ActivityLogsRawRecords* 的中繼資料表，並針對診斷計量和記錄擷取至名為 *DiagnosticRawRecords* 的中繼資料表。 屆時會操作並展開資料。 使用更新原則時，會針對活動記錄將展開的資料擷取至 *ActivityLogs* 資料表、診斷計量的資料會擷取至 *DiagnosticMetrics*，診斷記錄的資料則擷取至 *DiagnosticLogs*。 這表示您必須建立兩個不同的資料表用以擷取活動記錄，並建立三個不同的資料表用以擷取診斷計量和記錄。

使用 Azure 資料總管 Web UI，在 Azure 資料總管資料庫中建立目標資料表。

# <a name="diagnostic-metrics"></a>[診斷計量](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>建立診斷計量的資料表

1. 在 *TestDatabase* 資料庫中建立名為 *DiagnosticMetrics* 的資料表，用以儲存診斷計量記錄。 請使用下列 `.create table` 控制命令：

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. 選取 [執行]  以建立資料表。

    ![執行查詢](media/ingest-data-no-code/run-query.png)

1. 使用下列查詢，在 *TestDatabase* 資料庫中建立名為 *DiagnosticRawRecords* 的中繼資料資料表，用來處理資料。 選取 [執行]  以建立資料表。

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. 為中繼資料表設定零[保留原則](kusto/management/retention-policy.md)：

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logs"></a>[診斷記錄](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>建立診斷記錄的資料表 

1. 在 *TestDatabase* 資料庫中建立名為 *DiagnosticLogs* 的資料表，用以儲存診斷記錄的記錄。 請使用下列 `.create table` 控制命令：

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. 選取 [執行]  以建立資料表。

1. 使用下列查詢，在 *TestDatabase* 資料庫中建立名為 *DiagnosticRawRecords* 的中繼資料資料表，用來處理資料。 選取 [執行]  以建立資料表。

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. 為中繼資料表設定零[保留原則](kusto/management/retention-policy.md)：

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logs"></a>[活動記錄](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>建立活動記錄的資料表 

1. 在 *TestDatabase* 資料庫中建立名為 *ActivityLogs* 的資料表，用以接收活動記錄的記錄。 若要建立資料表，請執行下列 Azure 資料總管查詢：

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. 在 *TestDatabase* 資料庫中建立名為 *ActivityLogsRawRecords* 中繼資料資料表，以便操作資料：

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. 為中繼資料表設定零[保留原則](kusto/management/retention-policy.md)：

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>建立資料表對應

 由於資料格式為 `json`，因此需要資料對應。 `json` 對應會將每個 json 路徑對應至資料表資料行名稱。

# <a name="diagnostic-metrics--diagnostic-logs"></a>[診斷計量/診斷記錄](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>將診斷計量和記錄對應至資料表

若要將診斷計量和記錄資料對應至資料表，請使用下列查詢：

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logs"></a>[活動記錄](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>將活動記錄對應至資料表

若要將活動記錄資料對應至資料表，請使用下列查詢：

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>建立計量和記錄資料的更新原則

# <a name="diagnostic-metrics"></a>[診斷計量](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>建立診斷計量的資料更新原則

1. 建立可展開診斷計量記錄集合的[函式](kusto/management/functions.md)，讓集合中的每個值能夠取得不同的資料列。 使用 [`mv-expand`](kusto/query/mvexpandoperator.md) 運算子：
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events['count']),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. 將[更新原則](kusto/management/updatepolicy.md)新增至目標資料表。 此原則會對 *DiagnosticRawRecords* 中繼資料資料表中任何新擷取的資料自動執行查詢，並將其結果擷取至 *DiagnosticMetrics* 資料表：

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="diagnostic-logs"></a>[診斷記錄](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>建立診斷記錄的資料更新原則

1. 建立可展開診斷記錄之記錄集合的[函式](kusto/management/functions.md)，讓集合中的每個值能夠取得不同的資料列。 您將在 Azure 資料總管叢集上啟用擷取記錄，並使用[擷取記錄結構描述](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema)。 您將建立一個用於成功和失敗擷取的資料表，而某些欄位將是空的，以供成功的擷取使用 (例如 ErrorCode)。 使用 [`mv-expand`](kusto/query/mvexpandoperator.md) 運算子：

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Result = tostring(events.resultType),
            OperationId = tostring(events.properties.OperationId),
            Database = tostring(events.properties.Database),
            Table = tostring(events.properties.Table),
            IngestionSourceId = tostring(events.properties.IngestionSourceId),
            IngestionSourcePath = tostring(events.properties.IngestionSourcePath),
            RootActivityId = tostring(events.properties.RootActivityId),
            ErrorCode = tostring(events.properties.ErrorCode),
            FailureStatus = tostring(events.properties.FailureStatus),
            Details = tostring(events.properties.Details)
    }
    ```

2. 將[更新原則](kusto/management/updatepolicy.md)新增至目標資料表。 此原則會對 *DiagnosticRawRecords* 中繼資料資料表中任何新擷取的資料自動執行查詢，並將其結果擷取至 *DiagnosticLogs* 資料表：

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="activity-logs"></a>[活動記錄](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>建立活動記錄的資料更新原則

1. 建立可展開活動記錄之記錄集合的[函式](kusto/management/functions.md)，讓集合中的每個值都能收到不同的資料列。 使用 [`mv-expand`](kusto/query/mvexpandoperator.md) 運算子：

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Category = tostring(events.category),
            ResultType = tostring(events.resultType),
            ResultSignature = tostring(events.resultSignature),
            DurationMs = toint(events.durationMs),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events.location),
            Level = tostring(events.level)
    }
    ```

2. 將[更新原則](kusto/management/updatepolicy.md)新增至目標資料表。 此原則會對 *ActivityLogsRawRecords* 中繼資料資料表中任何新擷取的資料自動執行查詢，並將其結果擷取至 *ActivityLogs* 資料表中：

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>建立 Azure 事件中樞命名空間

Azure 診斷設定能夠將計量和記錄匯出至儲存體帳戶或事件中樞。 在本教學課程中，我們將透過事件中樞來路由計量和記錄。 您將在下列步驟中建立診斷計量和記錄的事件中樞命名空間和事件中樞。 Azure 監視器會建立活動記錄的事件中樞 *insights-operational-logs*。

1. 在 Azure 入口網站中使用 Azure Resource Manager 範本建立事件中樞。 若要依照本文中的其餘步驟操作，請以滑鼠右鍵按一下 [部署至 Azure]  按鈕，然後選取 [在新視窗中開啟]  。 [部署至 Azure]  按鈕可將您帶往 Azure 入口網站。

    [![部署至 Azure 按鈕](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. 建立診斷記錄的事件中樞命名空間和事件中樞。

    ![建立事件中樞](media/ingest-data-no-code/event-hub.png)

1. 在表單中填寫以下資訊。 對於下表中未列出的任何設定，請使用預設值。

    **設定** | **建議的值** | **說明**
    |---|---|---|
    | **訂用帳戶** | *您的訂用帳戶* | 選取您要用於事件中樞的 Azure 訂用帳戶。|
    | **資源群組** | *test-resource-group* | 建立新的資源群組。 |
    | **位置** | 選取最符合您需求的區域。 | 在與其他資源相同的位置建立事件中樞命名空間。
    | **命名空間名稱** | *AzureMonitoringData* | 選擇可識別您命名空間的唯一名稱。
    | **事件中樞名稱** | *DiagnosticData* | 事件中樞位於命名空間之下，其會提供專屬的唯一範圍容器。 |
    | **取用者群組名稱** | *adxpipeline* | 建立取用者群組名稱。 取用者群組能讓多個取用應用程式各自擁有獨立的事件串流檢視。 |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>將 Azure 監視器計量和記錄連線至事件中樞

現在，您必須將診斷計量和記錄與活動記錄連線至事件中樞。

# <a name="diagnostic-metrics--diagnostic-logs"></a>[診斷計量/診斷記錄](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>將診斷計量和記錄連線至您的事件中樞

選取要從中匯入計量的資源。 有數個資源類型支援匯出診斷資料的作業，包括事件中樞命名空間、Azure Key Vault、Azure IoT 中樞和 Azure 資料總管叢集。 在本教學課程中，我們將使用 Azure 資料總管叢集作為資源，我們會檢閱查詢效能計量和擷取結果記錄。

1. 在 Azure 入口網站中選取您的 Kusto 叢集。
1. 選取 [診斷設定]  ，然後選取 [開啟診斷]  連結。 

    ![診斷設定](media/ingest-data-no-code/diagnostic-settings.png)

1. [診斷設定]  窗格隨即開啟。 請執行下列步驟：
   1. 將您的診斷記錄資料命名為 *ADXExportedData*。
   1. 在 [記錄]  底下，選取 [SucceededIngestion]  和 [FailedIngestion]  核取方塊。
   1. 在 [計量]  底下，選取 [查詢效能]  核取方塊。
   1. 選取 [串流至事件中樞]  核取方塊。
   1. 選取 [設定]  。

      ![診斷設定窗格](media/ingest-data-no-code/diagnostic-settings-window.png)

1. 在 [選取事件中樞]  窗格中，設定如何將診斷記錄中的資料匯出至您所建立的事件中樞：
    1. 在 [選取事件中樞命名空間]  清單中，選取 [AzureMonitoringData]  。
    1. 在 [選取事件中樞名稱]  清單中，選取 [DiagnosticData]  。
    1. 在 [選取事件中樞原則名稱]  清單中，選取 [RootManagerSharedAccessKey]  。
    1. 選取 [確定]  。

1. 選取 [儲存]  。

# <a name="activity-logs"></a>[活動記錄](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>將活動記錄連線到事件中樞

1. 在 Azure 入口網站的左側功能表中，選取 [活動記錄]  。
1. [活動記錄]  視窗隨即開啟。 選取 [匯出至事件中樞]  。

    ![活動記錄視窗](media/ingest-data-no-code/activity-log.png)

1. [匯出活動記錄]  視窗隨即開啟：
 
    ![匯出活動記錄視窗](media/ingest-data-no-code/export-activity-log.png)

1. 在 [匯出活動記錄]  視窗中，採取下列步驟：
      1. 選取您的訂用帳戶。
      1. 在 [區域]  清單中，選擇 [全選]  。
      1. 選取 [匯出至事件中樞]  核取方塊。
      1. 選擇 [選取服務匯流排命名空間]  ，以開啟 [選取事件中樞]  窗格。
      1. 在 [選取事件中樞]  窗格中，選取您的訂用帳戶。
      1. 在 [選取事件中樞命名空間]  清單中，選取 [AzureMonitoringData]  。
      1. 在 [選取事件中樞原則名稱]  清單中，選取預設的事件中樞原則名稱。
      1. 選取 [確定]  。
      1. 在視窗的左上角，選取 [儲存]  。
   將會建立名稱為 *insights-operational-logs* 的事件中樞。
---

### <a name="see-data-flowing-to-your-event-hubs"></a>查看流向事件中樞的資料

1. 等候幾分鐘，讓定義連線以及將活動記錄匯出至事件中樞的作業完成。 移至您的事件中樞命名空間，查看您所建立的事件中樞。

    ![建立的事件中樞](media/ingest-data-no-code/event-hubs-created.png)

1. 查看流向事件中樞的資料：

    ![事件中樞的資料](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>將事件中樞連線至 Azure 資料總管

現在，您必須建立診斷計量和記錄與活動記錄的資料連線。

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>建立診斷計量和記錄與活動記錄的資料連線

1. 在您名為 *kustodocs* 的 Azure 資料總管叢集中，選取左側功能表中的 [資料庫]  。
1. 在 [資料庫]  視窗中，選取您的 *TestDatabase* 資料庫。
1. 在左側功能表中，選取 [資料擷取]  。
1. 在 [資料擷取]  視窗中，按一下 [+ 新增資料連線]  。
1. 在 [資料連線]  視窗中，輸入下列資訊：

    ![事件中樞資料連線](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logs"></a>[診斷計量/診斷記錄](#tab/diagnostic-metrics+diagnostic-logs) 

1. 在 [資料連線]  視窗中使用下列設定：

    資料來源：

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | **資料連線名稱** | *DiagnosticsLogsConnection* | 您想要在 Azure 資料總管中建立的連線名稱。|
    | **事件中樞命名空間** | *AzureMonitoringData* | 您先前選擇用來辨識命名空間的名稱。 |
    | **事件中樞** | *DiagnosticData* | 您建立的事件中樞。 |
    | **取用者群組** | *adxpipeline* | 在您所建立事件中樞中定義的取用者群組。 |
    | | |

    目標資料表：

    路由有兩個選項：*靜態*和*動態*。 在本教學課程中，您將使用靜態路由 (預設值)，而指定資料表名稱、資料格式和對應。 將 [我的資料包含路由資訊]  保留為未選取。

     **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | **Table** | *DiagnosticRawRecords* | 您在 *TestDatabase* 資料庫中建立的資料表。 |
    | **資料格式** | *JSON* | 在資料表中使用的格式。 |
    | **資料行對應** | *DiagnosticRawRecordsMapping* | 您在 *TestDatabase* 資料庫中建立的對應，會將傳入的 JSON 資料對應至 *DiagnosticRawRecords* 資料表的資料行名稱與資料類型。|
    | | |

1. 選取 [建立]  。  

# <a name="activity-logs"></a>[活動記錄](#tab/activity-logs)

1. 在 [資料連線]  視窗中使用下列設定：

    資料來源：

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | **資料連線名稱** | *ActivityLogsConnection* | 您想要在 Azure 資料總管中建立的連線名稱。|
    | **事件中樞命名空間** | *AzureMonitoringData* | 您先前選擇用來辨識命名空間的名稱。 |
    | **事件中樞** | *insights-operational-logs* | 您建立的事件中樞。 |
    | **取用者群組** | *$Default* | 預設取用者群組。 如有需要，您可以建立不同的取用者群組。 |
    | | |

    目標資料表：

    路由有兩個選項：*靜態*和*動態*。 在本教學課程中，您將使用靜態路由 (預設值)，而指定資料表名稱、資料格式和對應。 將 [我的資料包含路由資訊]  保留為未選取。

     **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | **Table** | *ActivityLogsRawRecords* | 您在 *TestDatabase* 資料庫中建立的資料表。 |
    | **資料格式** | *JSON* | 在資料表中使用的格式。 |
    | **資料行對應** | *ActivityLogsRawRecordsMapping* | 您在 *TestDatabase* 資料庫中建立的對應，會將傳入的 JSON 資料對應至 *ActivityLogsRawRecords* 資料表的資料行名稱與資料類型。|
    | | |

1. 選取 [建立]  。  
---

## <a name="query-the-new-tables"></a>查詢新的資料表

您現在已有具備資料流程的管線。 根據預設，透過叢集擷取需要 5 分鐘的時間，因此請等待幾分鐘讓資料完成傳送，再開始查詢。

# <a name="diagnostic-metrics"></a>[診斷計量](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>查詢診斷計量資料表

下列查詢會從 Azure 資料總管中的診斷計量記錄分析查詢持續時間資料：

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

查詢結果：

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

# <a name="diagnostic-logs"></a>[診斷記錄](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>查詢診斷記錄資料表

此管線會透過事件中樞產生擷取。 您將檢閱這些擷取的結果。
下列查詢會分析一分鐘內增加的擷取數，包含 `Database`、`Table` 和 `IngestionSourcePath` 在每個間隔內的範例：

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

查詢結果：

|   |   |
| --- | --- |
|   |  count_ | any_Database | any_Table | any_IngestionSourcePath
|   | 00:06.156 | TestDatabase | DiagnosticRawRecords | `https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip`
| | |

# <a name="activity-logs"></a>[活動記錄](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>查詢活動記錄資料表

下列查詢會分析 Azure 資料總管活動記錄中的資料：

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

查詢結果：

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

---

## <a name="next-steps"></a>後續步驟

* 了解如何使用[撰寫 Azure 資料總管的查詢](write-queries.md)，對您從 Azure 資料總管擷取的資料撰寫更多查詢。
* [使用診斷記錄來監視 Azure 資料總管擷取作業](using-diagnostic-logs.md)
* [使用計量來監視叢集健康情況](using-metrics.md)
