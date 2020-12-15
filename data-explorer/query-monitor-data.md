---
title: 使用 Azure 資料總管來查詢 Azure 監視器中的資料 (預覽)
description: 在本主題中，您可以使用 (Application Insights 和 Log Analytics) 建立用於跨產品查詢的 Azure 資料總管，以在 Azure 監視器中查詢資料。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 12/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 607bcc7b1c0401116a94a9c06ff308a06ad936b7
ms.sourcegitcommit: d9e203a54b048030eeb6d05b01a65902ebe4e0b8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371518"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>使用 Azure 資料總管來查詢 Azure 監視器中的資料 (預覽)

Azure 資料總管支援 Azure 資料總管、[Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview) 和 [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) 之間的跨服務查詢。 您可以使用 Azure 資料總管查詢工具和跨服務查詢來查詢您的 Log Analytics 或 Application Insights 工作區。 本文說明如何建立跨服務查詢，並將 Log Analytics 或 Application Insights 工作區新增至 Azure 資料總管 Web UI。

Azure 資料總管跨服務查詢流程：

![Azure 資料總管 Proxy 的流程](media/query-monitor-data/query-monitor-workflow.png)

> [!NOTE]
> * 在預覽模式中，您可以直接使用 Azure 資料總管用戶端工具或在 Azure 資料總管叢集上執行查詢，從 Azure 資料總管查詢 Azure 監視器資料的能力。
>* 如需協助，請聯絡[跨服務查詢小組](mailto:adxproxy@microsoft.com)。


## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>將 Log Analytics/Application Insights 工作區新增至 Azure 資料總管用戶端工具

將 Log Analytics 或 Application Insights 工作區新增至 Azure 資料總管用戶端工具，為您的叢集啟用跨服務查詢。

1. 在連線到 Log Analytics 或 Application Insights 叢集之前，請先確認您的 Azure 資料總管原生叢集 (例如 help 叢集) 已出現在左側功能表上。

    ![Azure 資料總管原生叢集](media/query-monitor-data/web-ui-help-cluster.png)

1. 在 Azure 資料總管 UI (https://dataexplorer.azure.com/clusters) 中，選取 [新增叢集]。

1. 在 [新增叢集] 視窗中，新增 LA 或 AI 叢集的 URL。

    * LA：`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * AI：`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

1. 選取 [新增]  。

    ![新增叢集](media/query-monitor-data/add-cluster.png)

    >[!TIP]
    >如果您將連線新增至一個以上的 Log Analytics 或 Application Insights 工作區，請提供不同的名稱給每個連接。 否則，左窗格中的名稱會全部相同。

1. 建立連線之後，您的 Log Analytics 或 Application Insights 工作區將會出現在左窗格中，並顯示您的原生 Azure 資料總管叢集。

    ![Log Analytics 和 Azure 資料總管叢集](media/query-monitor-data/la-adx-clusters.png)

> [!NOTE]
> 可以對應的 Azure 監視器工作區數目上限為 100 個。

## <a name="run-queries"></a>執行查詢

您可以使用支援 Kusto 查詢的用戶端工具來執行查詢，例如：Kusto Explorer、Azure 資料總管 Web UI、Jupyter Kqlmagic、Flow、PowerQuery、PowerShell、Lens、REST API。

> [!NOTE]
> 跨服務查詢功能僅用於資料擷取。 如需詳細資訊，請參閱[函式支援性](#function-supportability)。

> [!TIP]
> * 資料庫應該與跨服務查詢中指定的資源名稱相同。 名稱區分大小寫。
> * 在跨服務查詢中，請確定 Application Insights 應用程式和 Log Analytics 工作區的命名是正確的。
> * 如果名稱包含特殊字元，則會以跨服務查詢中的 URL 編碼來取代。
> * 如果名稱中包含不符合 [KQL 識別碼名稱規則](kusto/query/schema-entities/entity-names.md)的字元，則會以破折號 **-** 字元來取代。

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>從 Azure 資料總管用戶端工具直接查詢您的 Log Analytics 或 Application Insights 工作區

您可以從 Azure 資料總管用戶端工具在您的 Log Analytics 或 Application Insights 工作區上執行查詢。 

1. 確認左側窗格中已選取您的工作區。

1. 執行下列查詢：

```kusto
Perf | take 10 // Demonstrate cross-service query on the Log Analytics workspace
```

![查詢 Log Analytics 工作區](media/query-monitor-data/query-la.png)

### <a name="cross-query-of-your-log-analytics-or-application-insights-workspace-and-the-azure-data-explorer-native-cluster"></a>跨查詢 Log Analytics 或 Application Insights 工作區和 Azure 資料總管原生叢集

當您執行跨叢集服務查詢時，請確認左側窗格中已選取您的 Azure 資料總管原生叢集。 下列範例會示範如何將 Azure 資料總管叢集資料表 (使用 `union`) 與 Log Analytics 工作區結合。

執行下列查詢：

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [ ![從 Azure 資料總管進行跨服務查詢](media/query-monitor-data/cross-query.png)](media/query-monitor-data/cross-query.png#lightbox)

> [!TIP]
> 若使用 [`join` 運算子](kusto/query/joinoperator.md) (而不是 union)，可能需要 [`hint`](kusto/query/joinoperator.md#join-hints) 在 Azure 資料總管原生叢集上執行該運算子。

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>將一個租用戶中的 Azure 資料總管叢集資料與另一個租用戶中的 Azure 監視器資源資料聯結

不支援在服務之間進行跨租用戶查詢。 您會登入單一租用戶來執行橫跨這兩個資源的查詢。

如果 Azure 資料總管資源是在租用戶 'A' 中，而 Log Analytics 工作區是在租用戶 'B' 中，請使用下列兩種方法的其中一種：

1. Azure 資料總管可讓您為不同租用戶中的主體新增角色。 將租用戶 'B' 中的使用者識別碼新增為 Azure 資料總管叢集上的授權使用者。 驗證 Azure 資料總管叢集上的 ['TrustedExternalTenant'](/powershell/module/az.kusto/update-azkustocluster) 屬性是否包含租用戶 'B'。 在租用戶 'B' 中完整地執行交叉查詢。

2. 使用 [Lighthouse](/azure/lighthouse/) 將 Azure 監視器資源投射到租用戶 'A'。

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>從不同的租用戶連線到 Azure 資料總管叢集

Kusto Explorer 會自動將您登入使用者帳戶原先所屬的租用戶。 若要使用相同的使用者帳戶存取其他租用戶中的資源，必須在連接字串中明確指定 `tenantId`：`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=`**TenantId**

## <a name="function-supportability"></a>函式支援性

Azure 資料總管跨服務查詢支援 Application Insights 和 Log Analytics 的函式。
這項功能可讓跨叢集查詢直接參考 Azure 監視器表格式函式。
跨服務查詢支援下列命令：

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

下圖描述從 Azure 資料總管 Web UI 查詢表格式函式的範例。
若要使用函式，請在查詢視窗中執行名稱。

  [ ![從 Azure 資料總管 Web UI 查詢表格式函式](media/query-monitor-data/function-query.png)](media/query-monitor-data/function-query.png#lightbox)

## <a name="additional-syntax-examples"></a>其他語法範例

呼叫 Application Insights 或 Log Analytics 叢集時，可以使用下列語法選項：

|語法描述  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 資料庫位在只包含此訂用帳戶中已定義資源的叢集中 (**建議用於跨叢集查詢**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| 包含此訂用帳戶中所有應用程式/工作區的叢集    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|叢集包含訂用帳戶中屬於此資源群組成員的所有應用程式/工作區    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|僅包含此訂用帳戶中已定義資源的叢集      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>後續步驟

[撰寫查詢](write-queries.md)