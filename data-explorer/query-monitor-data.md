---
title: 使用 Azure 資料總管來查詢 Azure 監視器中的資料 (預覽)
description: 在本主題中，您可以使用 Application Insights 和 Log Analytics 建立用於交叉乘積查詢的 Azure 資料總管 Proxy，以在 Azure 監視器中查詢資料
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/28/2020
ms.localizationpriority: high
ms.openlocfilehash: cd0bc28a2d2b282c50a85c87dbf8f4989c7b4057
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513211"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>使用 Azure 資料總管來查詢 Azure 監視器中的資料 (預覽)

在 [Azure 監視器](/azure/azure-monitor/)服務中，Azure 資料總管 Proxy 叢集 (ADX Proxy) 是可讓您在 Azure 資料總管、[Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview) 和 [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) 之間執行交叉乘積查詢的實體。 您可以將 Azure 監視器的 Log Analytics 工作區或 Application Insights 應用程式對應為 Proxy 叢集。 接著，您可以使用 Azure 資料總管工具來查詢 Proxy 叢集，並在跨叢集查詢中參考此叢集。 本文將說明如何連線到 Proxy 叢集、將 Proxy 叢集新增至 Azure 資料總管 Web UI，以及從 Azure 資料總管對您的 AI 應用程式或 LA 工作區執行查詢。

Azure 資料總管 Proxy 的流程：

![ADX Proxy 的流程](media/adx-proxy/adx-proxy-workflow.png)

## <a name="prerequisites"></a>先決條件

> [!NOTE]
> ADX Proxy 目前處於預覽模式。 [連線到 Proxy](#connect-to-the-proxy)，為您的叢集啟用 ADX Proxy 功能。 如有任何問題，請洽詢 [ADXProxy](mailto:adxproxy@microsoft.com) 團隊。

## <a name="connect-to-the-proxy"></a>連線到 Proxy

1. 在連線到 Log Analytics 或 Application Insights 叢集之前，請先確認您的 Azure 資料總管原生叢集 (例如 help 叢集) 已出現在左側功能表上。

    ![ADX 原生叢集](media/adx-proxy/web-ui-help-cluster.png)

1. 在 Azure 資料總管 UI (https://dataexplorer.azure.com/clusters) 中，選取 [新增叢集]。

1. 在 [新增叢集] 視窗中，新增 LA 或 AI 叢集的 URL。 
    
    * LA：`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * AI：`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * 選取 [新增]  。

    ![新增叢集](media/adx-proxy/add-cluster.png)

    >[!NOTE]
    >如果您對多個 Proxy 叢集新增連線，請為每個叢集提供不同的名稱。 否則，左窗格中的名稱會全部相同。

1. 建立連線之後，您的 LA 或 AI 叢集將會與您的原生 ADX 叢集一起出現在左側窗格中。 

    ![Log Analytics 和 Azure 資料總管叢集](media/adx-proxy/la-adx-clusters.png)

> [!NOTE]
> 可以對應的 Azure 監視器工作區數目上限為 100 個。

## <a name="run-queries"></a>執行查詢

您可以使用支援 Kusto 查詢的用戶端工具來執行查詢，例如：Kusto Explorer、ADX Web UI、Jupyter Kqlmagic、Flow、PowerQuery、PowerShell、Jarvis、Lens、REST API。

> [!NOTE]
> ADX Proxy 功能僅適用於資料擷取。 如需詳細資訊，請參閱[函式支援性](#function-supportability)。

> [!TIP]
> * 資料庫名稱應該與 Proxy 叢集中指定的資源名稱相同。 名稱區分大小寫。
> * 在跨叢集查詢中，請確定 Application Insights 應用程式和 Log Analytics 工作區的命名是正確的。
> * 如果名稱包含特殊字元，則會以 Proxy 叢集名稱中的 URL 編碼來取代。 
> * 如果名稱中包含不符合 [KQL 識別碼名稱規則](kusto/query/schema-entities/entity-names.md)的字元，則會以破折號 **-** 字元來取代。

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>從 LA 或 AI ADX Proxy 叢集直接查詢

在您的 LA 或 AI 叢集上執行查詢。 確認左側窗格中已選取您的叢集。 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![查詢 LA 工作區](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>對 LA 或 AI ADX Proxy 叢集和 ADX 原生叢集進行交叉查詢

當您從 Proxy 執行跨叢集查詢時，請確認左側窗格中已選取您的 ADX 原生叢集。 下列範例會示範如何將 ADX 叢集資料表 (使用 `union`) 與 LA 工作區結合。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [ ![從 Azure 資料總管 Proxy 進行交叉查詢](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

若使用 [`join` 運算子](kusto/query/joinoperator.md)(而不是 union)，可能需要 [`hint`](kusto/query/joinoperator.md#join-hints) 在 Azure 資料總管原生叢集上執行該運算子 (而不是在 Proxy 上)。 

### <a name="join-data-from-an-adx-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>將一個租用戶中的 ADX 叢集資料與另一個租用戶中的 Azure 監視器資源資料聯結

ADX Proxy 不支援跨租用戶的查詢。 您會登入單一租用戶來執行橫跨這兩個資源的查詢。

如果 Azure 資料總管資源是在租用戶 'A' 中，而 LA 工作區是在租用戶 'B' 中，請使用下列兩種方法的其中一種：

1. Azure 資料總管可讓您為不同租用戶中的主體新增角色。 將租用戶 'B' 中的使用者識別碼新增為 Azure 資料總管叢集上的授權使用者。 驗證 Azure 資料總管叢集上的 ['TrustedExternalTenant'](/powershell/module/az.kusto/update-azkustocluster) 屬性是否包含租用戶 'B'。 在租用戶 'B' 中完整地執行交叉查詢。

2. 使用 [Lighthouse](/azure/lighthouse/) 將 Azure 監視器資源投射到租用戶 'A'。

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>從不同的租用戶連線到 Azure 資料總管叢集

Kusto Explorer 會自動將您登入使用者帳戶原先所屬的租用戶。 若要使用相同的使用者帳戶存取其他租用戶中的資源，必須在連接字串中明確指定 `tenantId`：`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>函式支援性

Azure 資料總管 Proxy 叢集支援 Application Insights 和 Log Analytics 的函式。
這項功能可讓跨叢集查詢直接參考 Azure 監視器表格式函式。
Proxy 支援下列命令：

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

下圖描述從 Azure 資料總管 Web UI 查詢表格式函式的範例。
若要使用函式，請在查詢視窗中執行名稱。

  [ ![從 Azure 資料總管 Web UI 查詢表格式函式](media/adx-proxy/function-query-adx-proxy.png)](media/adx-proxy/function-query-adx-proxy.png#lightbox)


## <a name="additional-syntax-examples"></a>其他語法範例

呼叫 Application Insights (AI) 或 Log Analytics (LA) 叢集時，可以使用下列語法選項：

|語法描述  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 資料庫位在只包含此訂用帳戶中已定義資源的叢集中 (**建議用於跨叢集查詢**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| 包含此訂用帳戶中所有應用程式/工作區的叢集    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|叢集包含訂用帳戶中屬於此資源群組成員的所有應用程式/工作區    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|僅包含此訂用帳戶中已定義資源的叢集      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>後續步驟

[撰寫查詢](write-queries.md)