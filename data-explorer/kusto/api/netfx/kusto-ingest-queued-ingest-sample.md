---
title: 如何使用 Kusto.Ingest 函式庫進行資料引入 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了如何在 Azure 數據資源管理器中使用 Kusto.ingest 庫進行數據引入。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/05/2020
ms.openlocfilehash: 80b2b61c70269c5bd166a064fe9d0e2c59dd8197
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523625"
---
# <a name="howto-data-ingestion-with-kustoingest-library"></a>如何使用 Kusto.Ingest 函式庫進行資料引入
本文介紹了使用 Kusto.Ingest 用戶端庫的範例代碼。

## <a name="overview"></a>概觀
以下代碼示例演示了使用 Kusto.Ingest 庫將數據引入 Kusto 的數據佇列(透過 Kusto 資料管理服務)。

> 本文討論建議的生產級管道引入模式,也稱為**排隊引入**(在 Kusto.Ingest 庫中,相應的實體是[IKustoQueuedingestClient 介面](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient))。 在此模式下,客戶端代碼通過將引入通知消息發佈到 Azure 佇列與 Kusto 服務進行互動,該引用是從 Kusto 資料管理 (a.k.a.a) 獲得的引用。 攝入)服務。 與數據管理服務的交互必須與**AAD**進行身份驗證。

#### <a name="authentication"></a>驗證
此代碼示例使用 AAD 使用者身份驗證,並在互動式使用者的身份下運行。

## <a name="dependencies"></a>相依性
此範例碼需要以下 NuGet 套件:
* 微軟.庫斯特托.英格斯特
* Microsoft.IdentityModel.Clients.ActiveDirectory
* WindowsAzure.Storage
* Newtonsoft.Json

## <a name="namespaces-used"></a>使用的命名空間
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Kusto.Data;
using Kusto.Data.Common;
using Kusto.Data.Net.Client;
using Kusto.Ingest;
```

## <a name="code"></a>程式碼
下面介紹的代碼執行以下操作:
1. 在資料庫下的`KustoIngestClientDemo``KustoLab`分享 Kusto 叢集上建立表
2. 在該表上設定[JSON 列映射物件](../../management/create-ingestion-mapping-command.md)
3. 為`Ingest-KustoLab`資料管理服務建立[IKustoQueueddd 客戶端](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)實體
4. 使用適當的引入選項設定[KustoQueueinge 屬性](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)
5. 建立記憶體,填滿一些要引入的產生資料
6. 使用`KustoQueuedIngestClient.IngestFromStream`方法引入資料
7. 輪詢任何[引入錯誤](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient)

```csharp
static void Main(string[] args)
{
    var clusterName = "KustoLab";
    var db = "KustoIngestClientDemo";
    var table = "Table1";
    var mappingName = "Table1_mapping_1";
    var serviceNameAndRegion = "clusterNameAndRegion"; // For example, "mycluster.westus"
    var authority = "AAD Tenant or name"; // For example, "microsoft.com"

    // Set up table
    var kcsbEngine =
        new KustoConnectionStringBuilder($"https://{serviceNameAndRegion}.kusto.windows.net").WithAadUserPromptAuthentication(authority: $"{authority}");

    using (var kustoAdminClient = KustoClientFactory.CreateCslAdminProvider(kcsbEngine))
    {
        var columns = new List<Tuple<string, string>>()
        {
            new Tuple<string, string>("Column1", "System.Int64"),
            new Tuple<string, string>("Column2", "System.DateTime"),
            new Tuple<string, string>("Column3", "System.String"),
        };

        var command = CslCommandGenerator.GenerateTableCreateCommand(table, columns);
        kustoAdminClient.ExecuteControlCommand(databaseName: db, command: command);

        // Set up mapping
        var columnMappings = new List<JsonColumnMapping>();
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column1", JsonPath = "$.Id" });
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column2", JsonPath = "$.Timestamp" });
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column3", JsonPath = "$.Message" });

        command = CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
                                            table, mappingName, columnMappings);
        kustoAdminClient.ExecuteControlCommand(databaseName: db, command: command);
    }

    // Create Ingest Client
    var kcsbDM =
        new KustoConnectionStringBuilder($"https://ingest-{serviceNameAndRegion}.kusto.windows.net").WithAadUserPromptAuthentication(authority: $"{authority}");

    using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(kcsbDM))
    {
        var ingestProps = new KustoQueuedIngestionProperties(db, table);
        // For the sake of getting both failure and success notifications we set this to IngestionReportLevel.FailuresAndSuccesses
        // Usually the recommended level is IngestionReportLevel.FailuresOnly
        ingestProps.ReportLevel = IngestionReportLevel.FailuresAndSuccesses;
        ingestProps.ReportMethod = IngestionReportMethod.Queue;
        ingestProps.JSONMappingReference = mappingName;
        ingestProps.Format = DataSourceFormat.json;

        // Prepare data for ingestion
        using (var memStream = new MemoryStream())
        using (var writer = new StreamWriter(memStream))
        {
            for (int counter = 1; counter <= 10; ++counter)
            {
                writer.WriteLine(
                    "{{ \"Id\":\"{0}\", \"Timestamp\":\"{1}\", \"Message\":\"{2}\" }}",
                    counter, DateTime.UtcNow.AddSeconds(100 * counter),
                    $"This is a dummy message number {counter}");
            }

            writer.Flush();
            memStream.Seek(0, SeekOrigin.Begin);

            // Post ingestion message
            ingestClient.IngestFromStream(memStream, ingestProps, leaveOpen: true);
        }

        // Wait and retrieve all notifications
        //  - Actual duration should be decided based on the effective Ingestion Batching Policy set on the table/database
        Thread.Sleep(<timespan>);
        var errors = ingestClient.GetAndDiscardTopIngestionFailures().GetAwaiter().GetResult();
        var successes = ingestClient.GetAndDiscardTopIngestionSuccesses().GetAwaiter().GetResult();

        errors.ForEach((f) => { Console.WriteLine($"Ingestion error: {f.Info.Details}"); });
        successes.ForEach((s) => { Console.WriteLine($"Ingested: {s.Info.IngestionSourcePath}"); });
    }
}
```