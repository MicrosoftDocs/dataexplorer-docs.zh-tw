---
title: 使用 Azure 資料資源管理員在 Azure 監視器中查詢資料(預覽)
description: 在本主題中,透過建立 Azure 資料資源管理員代理來使用應用程式見解和紀錄分析為跨產品查詢,在 Azure 監視器中查詢資料
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 983a9af42772209df2f48c1b1480e8ff0f34b5d6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501382"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>使用 Azure 資料資源管理員在 Azure 監視器中查詢資料(預覽)

Azure 資料資源管理員代理群集 (ADX 代理) 是一個實體,使您能夠在[Azure 監視器](/azure/azure-monitor/)服務中執行 Azure 資料資源管理員、[應用程式見解 (AI)](/azure/azure-monitor/app/app-insights-overview)和[日誌分析 (LA)](/azure/azure-monitor/platform/data-platform-logs)之間的跨產品查詢。 您可以將 Azure 監視器日誌分析工作區或應用程式見解應用映射為代理群集。 然後,可以使用 Azure 資料資源管理員工具查詢代理群集,並在跨群集查詢中引用它。 本文演示如何連接到代理群集、向 Azure 資料資源管理器 Web UI 添加代理群集,以及從 Azure 資料資源管理器運行針對 AI 應用或 LA 工作區的查詢。

Azure 資料資源管理員代理流: 

![ADX 代理流](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Prerequisites

> [!NOTE]
> ADX 代理處於預覽模式。 [連接到代理](#connect-to-the-proxy)以為群集啟用 ADX 代理功能。 如有任何問題,請聯絡[ADXProxy](mailto:adxproxy@microsoft.com)團隊。

## <a name="connect-to-the-proxy"></a>連線到代理

1. 在連接到日誌分析或應用程式見解群集之前,驗證 Azure 資料資源管理器本機群集(如*説明*群集)顯示在左側功能表上。

    ![ADX 本機叢集](media/adx-proxy/web-ui-help-cluster.png)

1. 在 Azure 資料資源https://dataexplorer.azure.com/clusters)管理員 UI 中(中,選擇**添加群集**)。

1. 在 **"添加叢集'** 視窗中,將 URL 添加到 LA 或 AI 叢集。 
    
    * 對於洛杉磯:`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * 對 AI:`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * 選取 [新增]  。

    ![新增叢集](media/adx-proxy/add-cluster.png)

    如果向多個代理群集添加連接,請為每個組合指定不同的名稱。 否則,它們在左側窗格中將具有相同的名稱。

1. 建立連接後,LA 或 AI 群集將顯示在左側窗格中,並帶有本機 ADX 群集。 

    ![紀錄分析與 Azure 資料資源管理器叢集](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>執行查詢

您可以使用支援 Kusto 查詢的用戶端工具執行查詢,例如:庫斯特資源管理器、ADX Web UI、Jupyter Kqlmagic、Flow、PowerQuery、PowerShell、Jarvis、鏡頭、REST API。

> [!TIP]
> * 資料庫名稱應具有與代理群集中指定的資源相同的名稱。 名稱區分大小寫。
> * 在跨群集查詢中,請確保應用程式見解應用和日誌分析工作區的命名正確。
>     * 如果名稱包含特殊字元,則它們將被代理群集名稱中的 URL 編碼替換。 
>     * 如果名稱包含不符合[KQL 識別符名稱規則的](kusto/query/schema-entities/entity-names.md)字元,則它們將被**-** 虛線 字元替換。

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>從 LA 或 AI ADX 代理叢集的直接查詢

在 LA 或 AI 群集上運行查詢。 驗證群集是否在左側窗格中被選中。 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![查詢 LA 工作區](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>交叉查詢 LA 或 AI ADX 代理叢集和 ADX 本機群集 

從代理運行跨群集查詢時,請驗證在左側窗格中選擇 ADX 本機群集。 以下範例展示將 ADX 群集`union`表 (使用 ) 與 LA 工作區相結合。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![來自 Azure 資料資源管理員代理的交叉查詢](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

使用[`join`運算子](kusto/query/joinoperator.md)(而不是聯合) 可能[`hint`](kusto/query/joinoperator.md#join-hints)需要 在 Azure 資料資源管理員本機群集(而不是在代理上)上運行它。 

## <a name="additional-syntax-examples"></a>其他語法範例

呼叫應用程式的解解 (AI) 或紀錄分析 (LA) 叢集時, 可以使用以下文法選項:

|語法描述  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 群組中包含此訂閱中已定義資源的資料庫(**建議用於跨叢集查詢**) |   叢集(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | 叢集(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| 包含此訂閱中的所有應用程式、工作區的群集    |     叢集(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    叢集(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|包含訂閱中的所有應用程式、工作區且是此資源群組的成員的叢集    |   叢集(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    叢集(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|只包含此訂閱中已定義資源的叢集      |    叢集(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  叢集(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>後續步驟

[撰寫查詢](write-queries.md)
