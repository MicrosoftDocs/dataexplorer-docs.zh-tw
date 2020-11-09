---
title: 使用診斷記錄來監視 Azure 資料總管內嵌、命令和查詢
description: 瞭解如何設定 Azure 資料總管的診斷記錄，以監視內嵌命令和查詢作業。
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/16/2020
ms.openlocfilehash: 606ae915e822cf4f2c02ac590a5bb05bdb17f28a
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94373896"
---
# <a name="monitor-azure-data-explorer-ingestion-commands-and-queries-using-diagnostic-logs"></a>使用診斷記錄來監視 Azure 資料總管內嵌、命令和查詢

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料流。 [Azure 監視器診斷記錄](/azure/azure-monitor/platform/diagnostic-logs-overview) 可提供 Azure 資源作業的相關資料。 Azure 資料總管會使用診斷記錄來取得內嵌成功、內嵌失敗、命令和查詢作業的見解。 您可以將作業記錄匯出至 Azure 儲存體、事件中樞或 Log Analytics，以監視內嵌、命令和查詢狀態。 來自 Azure 儲存體和 Azure 事件中樞的記錄可以路由傳送到 Azure 資料總管叢集中的資料表，以供進一步分析。

> [!IMPORTANT] 
> 診斷記錄資料可能包含機密資料。 根據您的監視需求，限制記錄目的地的許可權。 

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請建立 [免費的 azure 帳戶](https://azure.microsoft.com/free/)。
* 登入 [Azure 入口網站](https://portal.azure.com/)。
* 建立叢集 [和資料庫](create-cluster-database-portal.md)。

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>設定 Azure 資料總管叢集的診斷記錄

診斷記錄可以用來設定下列記錄資料的集合：

# <a name="ingestion"></a>[擷取](#tab/ingestion)

> [!NOTE]
> 使用 Sdk、資料連線和連接器，可對內嵌端點的佇列內嵌支援內嵌記錄。
>
> 內嵌記錄不支援串流內嵌、直接內嵌到引擎、從查詢內嵌，或設定或附加命令。

* **成功** 的內嵌作業：這些記錄檔包含已成功完成內嵌作業的相關資訊。
* **失敗** 的內嵌作業：這些記錄檔具有失敗內嵌作業的詳細資訊，包括錯誤詳細資料。 

# <a name="commands-and-queries"></a>[命令和查詢](#tab/commands-and-queries)

* **命令** ：這些記錄檔包含已達到最終狀態之系統管理命令的相關資訊。
* **查詢** ：這些記錄檔會提供已達到最終狀態之查詢的詳細資訊。 

    > [!NOTE]
    > 查詢記錄資料不包含查詢文字。

---

然後，資料會封存至儲存體帳戶、串流至事件中樞，或根據您的規格傳送至 Log Analytics。

### <a name="enable-diagnostic-logs"></a>啟用診斷記錄

診斷記錄預設為停用。 若要啟用診斷記錄，請執行下列步驟：

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，選取您要監視的 Azure 資料總管叢集資源。
1. 在 [監視]  下方，選取 [診斷設定]  。
  
    ![新增診斷記錄](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. 選取 [ **新增診斷設定** ]。
1. 在 [ **診斷設定** ] 視窗中：

    :::image type="content" source="media/using-diagnostic-logs/configure-diagnostics-settings.png" alt-text="設定診斷設定":::

    1. 選取診斷設定的 **名稱** 。
    1. 選取一或多個目標：儲存體帳戶、事件中樞或 Log Analytics。
    1. 選取要收集的記錄： `SucceededIngestion` 、 `FailedIngestion` 、 `Command` 或 `Query` 。
    1. 選取要收集的 [計量](using-metrics.md#supported-azure-data-explorer-metrics) (選擇性) 。  
    1. 選取 [ **儲存** ]，以儲存新的診斷記錄設定和計量。

將會在幾分鐘內設定新的設定。 然後，記錄會出現在設定的封存目標中 (儲存體帳戶、事件中樞或 Log Analytics) 。 

> [!NOTE]
> 如果您將記錄傳送至 log analytics，則 `SucceededIngestion` 、、 `FailedIngestion` `Command` 和 `Query` 記錄會分別儲存在名為： `SucceededIngestion` 、 `FailedIngestion` 、、的 `ADXCommand` log analytics 資料表中 `ADXQuery` 。

## <a name="diagnostic-logs-schema"></a>診斷記錄結構描述

所有 [Azure 監視器診斷記錄都會共用通用的最上層架構](/azure/azure-monitor/platform/diagnostic-logs-schema)。 Azure 資料總管對自己的事件具有唯一的屬性。 所有記錄都以 JSON 格式儲存。

# <a name="ingestion"></a>[擷取](#tab/ingestion)

### <a name="ingestion-logs-schema"></a>內嵌記錄架構

記錄 JSON 字串包括下表所列的元素：

|Name               |描述
|---                |---
|time               |報表的時間
|resourceId         |Azure Resource Manager 資源識別碼
|operationName      |作業的名稱： ' MICROSOFT。KUSTO/叢集/內嵌/動作 '
|operationVersion   |架構版本： ' 1.0 ' 
|category           |作業的類別。 `SucceededIngestion` 或 `FailedIngestion`。 [成功](#successful-ingestion-operation-log)作業或[失敗](#failed-ingestion-operation-log)作業的屬性不同。
|properties         |作業的詳細資訊。

#### <a name="successful-ingestion-operation-log"></a>成功的內嵌作業記錄

**範例︰**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**成功作業診斷記錄的屬性**

|Name               |描述
|---                |---
|succeededOn        |內嵌完成的時間
|operationId        |Azure 資料總管內嵌作業識別碼
|[資料庫]           |目標資料庫的名稱
|資料表              |目標資料表的名稱
|ingestionSourceId  |內嵌資料來源的識別碼
|ingestionSourcePath|內嵌資料來源或 blob URI 的路徑
|rootActivityId     |活動識別碼

#### <a name="failed-ingestion-operation-log"></a>失敗的內嵌作業記錄

**範例︰**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**失敗作業診斷記錄的屬性**

|Name               |描述
|---                |---
|failedOn           |內嵌完成的時間
|operationId        |Azure 資料總管內嵌作業識別碼
|[資料庫]           |目標資料庫的名稱
|資料表              |目標資料表的名稱
|ingestionSourceId  |內嵌資料來源的識別碼
|ingestionSourcePath|內嵌資料來源或 blob URI 的路徑
|rootActivityId     |活動識別碼
|詳細資料            |失敗和錯誤訊息的詳細描述
|errorCode          |錯誤碼 
|failureStatus      |`Permanent` 或 `Transient`。 暫時性失敗的重試可能會成功。
|originatesFromUpdatePolicy|如果失敗源自更新原則，則為 True
|shouldRetry        |如果重試可能會成功，則為 True

# <a name="commands-and-queries"></a>[命令和查詢](#tab/commands-and-queries)

### <a name="commands-and-queries-logs-schema"></a>命令和查詢記錄架構

記錄 JSON 字串包括下表所列的元素：

|Name               |描述
|---                |---
|time               |報表的時間
|resourceId         |Azure Resource Manager 資源識別碼
|operationName      |作業的名稱： ' MICROSOFT。KUSTO/叢集/命令/動作 ' 或 "MICROSOFT。KUSTO/叢集/查詢/動作」。 命令和查詢記錄檔的屬性不同。
|operationVersion   |架構版本： ' 1.0 ' 
|category           |作業的類別： `Command` 或 `Query` 。 命令和查詢記錄檔的屬性不同。
|properties         |作業的詳細資訊。

#### <a name="command-log"></a>命令記錄檔

**範例︰**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/COMMAND/ACTION",
    "operationVersion": "1.0",
    "category": "Command",
    "properties":
    {
        "RootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d",
        "StartedOn": "2020-09-12T18:06:04.6898353Z",
        "LastUpdatedOn": "2020-09-12T18:06:04.6898353Z",
        "Database": "DatabaseSample",
        "State": "Completed",
        "FailureReason": "XXX",
        "TotalCpu": "00:01:30.1562500",
        "CommandType": "ExtentsDrop",
        "Application": "Kusto.WinSvc.DM.Svc",
        "ResourceUtilization": "{\"CacheStatistics\":{\"Memory\":{\"Hits\":0,\"Misses\":0},\"Disk\":{\"Hits\":0,\"Misses\":0},\"Shards\":{\"Hot\":{\"HitBytes\":0,\"MissBytes\":0,\"RetrieveBytes\":0},\"Cold\":{\"HitBytes\":0,\"MissBytes\":0,\"RetrieveBytes\":0},\"BypassBytes\":0}},\"TotalCpu\":\"00:00:00\",\"MemoryPeak\":0,\"ScannedExtentsStatistics\":{\"MinDataScannedTime\":null,\"MaxDataScannedTime\":null,\"TotalExtentsCount\":0,\"ScannedExtentsCount\":0,\"TotalRowsCount\":0,\"ScannedRowsCount\":0}}",
        "Duration": "00:03:30.1562500",
        "User": "AAD app id=0571b364-eeeb-4f28-ba74-90a8b4132b53",
        "Principal": "aadapp=0571b364-eeeb-4f28-ba74-90a3b4136b53;5c443533-c927-4410-a5d6-4d6a5443b64f"
    }
}
```
**命令診斷記錄的屬性**

|Name               |描述
|---                |---
|RootActivityId |根活動識別碼
|StartedOn        |此命令開始 (UTC) 時間
|LastUpdatedOn        |此命令結束的時間 (UTC) 
|資料庫          |命令執行所在的資料庫名稱
|State              |命令結束的狀態
|FailureReason  |失敗原因
|TotalCpu |總 CPU 持續時間
|CommandType     |命令類型
|Application     |叫用命令的應用程式名稱
|ResourceUtilization     |命令資源使用量
|Duration     |命令持續時間
|User     |叫用查詢的使用者
|主體     |叫用查詢的主體

#### <a name="query-log"></a>查詢記錄

**範例︰**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/QUERY/ACTION",
    "operationVersion": "1.0",
    "category": "Query",
    "properties": {
        "RootActivityId": "3e6e8814-e64f-455a-926d-bf16229f6d2d",
        "StartedOn": "2020-09-04T13:45:45.331925Z",
        "LastUpdatedOn": "2020-09-04T13:45:45.3484372Z",
        "Database": "DatabaseSample",
        "State": "Completed",
        "FailureReason": "[none]",
        "TotalCpu": "00:00:00",
        "ApplicationName": "MyApp",
        "MemoryPeak": 0,
        "Duration": "00:00:00.0165122",
        "User": "AAD app id=0571b364-eeeb-4f28-ba74-90a8b4132b53",
        "Principal": "aadapp=0571b364-eeeb-4f28-ba74-90a8b4132b53;5c823e4d-c927-4010-a2d8-6dda2449b6cf",
        "ScannedExtentsStatistics": {
            "MinDataScannedTime": "2020-07-27T08:34:35.3299941",
            "MaxDataScannedTime": "2020-07-27T08:34:41.991661",
            "TotalExtentsCount": 64,
            "ScannedExtentsCount": 64,
            "TotalRowsCount": 320,
            "ScannedRowsCount": 320
        },
        "CacheStatistics": {
            "Memory": {
                "Hits": 192,
                "Misses": 0
          },
            "Disk": {
                "Hits": 0,
                "Misses": 0
      },
            "Shards": {
                "Hot": {
                    "HitBytes": 0,
                    "MissBytes": 0,
                    "RetrieveBytes": 0
        },
                "Cold": {
                    "HitBytes": 0,
                    "MissBytes": 0,
                    "RetrieveBytes": 0
        },
            "BypassBytes": 0
      }
    },
    "ResultSetStatistics": {
        "TableCount": 1,
        "TablesStatistics": [
        {
          "RowCount": 1,
          "TableSize": 9
        }
      ]
    }
  }
}
```

**查詢診斷記錄的屬性**

|Name               |描述
|---                |---
|RootActivityId |根活動識別碼
|StartedOn        |此命令開始 (UTC) 時間
|LastUpdatedOn           |此命令結束的時間 (UTC) 
|資料庫              |命令執行所在的資料庫名稱
|State  |命令結束的狀態
|FailureReason|失敗原因
|TotalCpu     |總 CPU 持續時間
|ApplicationName            |叫用查詢的應用程式名稱
|MemoryPeak          |記憶體尖峰
|Duration      |命令持續時間
|User|叫用查詢的使用者
|主體        |叫用查詢的主體
|ScannedExtentsStatistics        | 包含掃描的範圍統計資料
|MinDataScannedTime        |最小資料掃描時間
|MaxDataScannedTime        |最大資料掃描時間
|TotalExtentsCount        |總範圍計數
|ScannedExtentsCount        |掃描的範圍計數
|TotalRowsCount        |總數據列計數
|ScannedRowsCount        |掃描的資料列計數
|CacheStatistics        |包含快取統計資料
|記憶體        |包含快取記憶體統計資料
|點擊        |記憶體快取點擊
|遺漏        |記憶體快取遺漏
|磁碟        |包含快取磁片統計資料
|點擊        |磁碟快取點擊
|遺漏        |磁碟快取遺漏
|碎片        |包含熱和冷分區快取統計資料
|經常性存取層        |包含熱分區快取統計資料
|HitBytes        |分區熱快取點擊
|MissBytes        |分區熱緩存遺漏
|RetrieveBytes        | 分區熱快取已取出位元組
|冷        |包含冷分區快取統計資料
|HitBytes        |分區冷快取點擊
|MissBytes        |分區冷快取遺漏
|RetrieveBytes        |分區冷快取已取出位元組
|BypassBytes        |分區快取略過位元組
|ResultSetStatistics        |包含結果集統計資料
|TableCount        |結果集資料表計數
|TablesStatistics        |包含結果集資料表統計資料
|RowCount        | 結果集資料表資料列計數
|TableSize        |結果集資料表資料列計數

---

## <a name="next-steps"></a>下一步

* [使用計量來監視叢集健康情況](using-metrics.md)
* [教學課程：在 Azure 資料總管中內嵌和查詢監視資料](ingest-data-no-code.md) 以內嵌診斷記錄
