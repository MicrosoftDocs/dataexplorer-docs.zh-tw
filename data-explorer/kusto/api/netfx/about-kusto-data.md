---
title: 庫斯托用戶端庫 - Azure 數據資源管理員 |微軟文件
description: 本文介紹了 Azure 資料資源管理器中的 Kusto 用戶端庫。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/15/2020
ms.openlocfilehash: 8a3ab9bb3727efdc80e1887ab802f2ad4e3c7cce
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502596"
---
# <a name="kusto-client-library"></a>庫斯托用戶端庫
    
Kusto 用戶端 SDK (Kusto.Data) 公開了類似於 ADO.NET 的程式設計 API,因此對於具有 .NET 經驗的用戶來說,使用它應該感覺很自然。 從指向 Kusto`ICslQueryProvider`引擎服務、資料庫、身份`ICslAdminProvider`驗證方法 等的連接字串物件建立查詢用戶端 ( ) 或控制命令提供者 ( ) 。然後,可以通過指定相應的 Kusto 查詢語言字串來發出數據查詢或控制命令,並`IDataReader`通過返回的物件返回一個或多個數據表。

更具體地說,要創建一個類似ADO.NET的用戶端,允許對 Kusto 進行查詢,請使用`Kusto.Data.Net.Client.KustoClientFactory`類上的 靜態方法。 這些採用連接字串並創建一個線程安全的一次性用戶端物件。 (強烈建議客戶端代碼不要創建此物件的"太多"實例。 相反,用戶端代碼應為每個連接字串創建一個物件,並根據需要保留該物件。這允許用戶端物件有效地緩存資源。

通常,用戶端上的所有方法都是線程安全的,但有兩個例外:`Dispose`和 setter 屬性。 對於一致的結果,不要同時調用任一方法。

以下是一些範例。 其他示例可[在此處](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client)找到。

**範例:計算行數**
 
以下代碼展示了在名為`StormEvents``Samples`的資料庫中名為的表的行計數:

```csharp
var client = Kusto.Data.Net.Client.KustoClientFactory.CreateCslQueryProvider("https://help.kusto.windows.net/Samples;Fed=true");
var reader = client.ExecuteQuery("StormEvents | count");
// Read the first row from reader -- it's 0'th column is the count of records in MyTable
// Don't forget to dispose of reader when done.
```

**範例:從 Kusto 叢集取得診斷資訊**

```csharp
var kcsb = new KustoConnectionStringBuilder(cluster URI here). WithAadUserPromptAuthentication();
using (var client = KustoClientFactory.CreateCslAdminProvider(kcsb))
{
    var diagnosticsCommand = CslCommandGenerator.GenerateShowDiagnosticsCommand();
    var objectReader = new ObjectReader<DiagnosticsShowCommandResult>(client.ExecuteControlCommand(diagnosticsCommand));
    DiagnosticsShowCommandResult diagResult = objectReader.ToList().FirstOrDefault();
    // DO something with the diagResult    
}
```



## <a name="the-kustoclientfactory-client-factory"></a>庫斯托客戶工廠客戶工廠

靜態類`Kusto.Data.Net.Client.KustoClientFactory`為使用 Kusto 的客戶端代碼的作者提供了主要入口點。 它提供了以下重要的靜態方法:

|方法                                      |傳回值                                |用於                                                      |
|--------------------------------------------|---------------------------------------|--------------------------------------------------------------|
|`CreateCslQueryProvider`                    |`ICslQueryProvider`                    |將查詢發送到 Kusto 引擎群集。                    |
|`CreateCslAdminProvider`                    |`ICslAdminProvider`                    |向 Kusto 群集(任何類型的)發送控制命令。    |
|`CreateRedirectProvider`                    |`IRedirectProvider`                    |為 Kusto 請求建立重定向 HTTP 回應訊息。|

