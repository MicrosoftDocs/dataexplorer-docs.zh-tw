---
title: 使用 Kusto 內嵌資料連結庫-Azure 資料總管
description: 本文說明如何使用 Azure 資料總管的 Kusto 程式庫來內嵌資料。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/05/2020
ms.openlocfilehash: fe268d19e5f42308737b7c392c58c6c1dca071b3
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799606"
---
# <a name="data-ingestion-with-the-kustoingest-library"></a>使用 Kusto 程式庫內嵌資料

本文提供的範例程式碼會使用 Kusto 的內嵌用戶端程式庫來內嵌資料。 此程式碼會詳細說明適用于生產等級管線（稱為佇列內嵌）的建議內嵌模式。 針對 Kusto 程式庫，對應的實體是[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)介面。 用戶端程式代碼會藉由將內嵌通知張貼到 Azure 佇列，與 Azure 資料總管服務互動。 佇列的參考是從負責內嵌的資料管理實體取得。 

> [!NOTE]
> 與資料管理服務的互動必須使用 Azure Active Directory （Azure AD）進行驗證。

此範例會使用 Azure AD 的使用者驗證，並在互動式使用者的身分識別下執行。

## <a name="dependencies"></a>相依性

此範例程式碼需要下列 NuGet 套件：
* Kusto。
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

此程式碼會執行下列動作。
1. 在`KustoLab` `KustoIngestClientDemo`資料庫下的共用 Azure 資料總管叢集上建立資料表
2. 在該資料表上布建[JSON 資料行對應物件](../../management/create-ingestion-mapping-command.md)
3. 建立`Ingest-KustoLab`資料管理服務的[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)實例
4. 使用適當的內嵌選項來設定[KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)
5. 建立 MemoryStream，並填入某些產生的資料以供內嵌
6. 使用`KustoQueuedIngestClient.IngestFromStream`方法來內嵌資料
7. 輪詢任何內嵌[錯誤](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient)

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
