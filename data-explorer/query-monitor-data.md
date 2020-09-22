---
title: '使用 Azure 資料總管查詢 Azure 監視器中的資料 (預覽) '
description: 在本主題中，藉由建立 Azure 資料總管 proxy 以使用 Application Insights 和 Log Analytics 進行跨產品查詢，來查詢 Azure 監視器中的資料
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: b8de71ffcda28a7baa0f8452e501c7485e861122
ms.sourcegitcommit: 5aba5f694420ade57ef24b96699d9b026cdae582
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90999005"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>使用 Azure 資料總管 Azure 監視器查詢資料 (預覽) 

Azure 資料總管 proxy 叢集 (ADX Proxy) 是一個實體，可讓您在 Azure 資料總管、 [Application Insights (AI) ](/azure/azure-monitor/app/app-insights-overview)，以及[ (](/azure/azure-monitor/)服務中的[Log Analytics) LA Azure 監視器](/azure/azure-monitor/platform/data-platform-logs)之間執行交叉乘積查詢。 您可以將 Azure 監視器 Log Analytics 工作區或 Application Insights 應用程式對應為 proxy 叢集。 然後，您可以使用 Azure 資料總管工具來查詢 proxy 叢集，並在跨叢集查詢中參考它。 本文說明如何連線到 proxy 叢集、將 proxy 叢集新增至 Azure 資料總管 Web UI，以及對您的 AI 應用程式或 Azure 資料總管的 LA 工作區執行查詢。

Azure 資料總管 proxy 流程： 

![ADX proxy 流程](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>必要條件

> [!NOTE]
> ADX Proxy 處於預覽模式。 [連接至 proxy](#connect-to-the-proxy) 以啟用叢集的 ADX proxy 功能。 如有任何問題，請洽詢 [ADXProxy](mailto:adxproxy@microsoft.com) 團隊。

## <a name="connect-to-the-proxy"></a>連接到 proxy

1. 在連線到 Log Analytics 或 Application Insights 叢集之前，請先確認您的 Azure 資料總管原生叢集 (（例如 *help* cluster) ）會出現在左側功能表中。

    ![ADX 原生叢集](media/adx-proxy/web-ui-help-cluster.png)

1. 在 Azure 資料總管 UI (中 https://dataexplorer.azure.com/clusters) ，選取 [ **新增**叢集]。

1. 在 [ **新增** 叢集] 視窗中，新增 LA 或 AI 叢集的 URL。 
    
    * 適用于 LA： `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * 針對 AI： `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * 選取 [新增]  。

    ![新增叢集](media/adx-proxy/add-cluster.png)

    >[!NOTE]
    >如果您將連線新增至多個 proxy 叢集，請為每個伺服器提供不同的名稱。 否則，它們會在左窗格中擁有相同的名稱。

1. 建立連線之後，您的 LA 或 AI 叢集將會出現在左窗格中，並顯示您的原生 ADX 叢集。 

    ![Log Analytics 和 Azure 資料總管叢集](media/adx-proxy/la-adx-clusters.png)

> [!NOTE]
> 可以對應的 Azure 監視器工作區數目限制為100。

## <a name="run-queries"></a>執行查詢

您可以使用支援 Kusto 查詢的用戶端工具來執行查詢，例如： Kusto Explorer、ADX Web UI、Jupyter Kqlmagic、Flow、PowerQuery、PowerShell、Jarvis、鏡頭 REST API。

> [!NOTE]
> ADX Proxy 功能僅供資料抓取之用。 如需詳細資訊，請參閱 [函數可支援](#function-supportability)性。

> [!TIP]
> * 資料庫名稱的名稱應該與在 proxy 叢集中指定的資源相同。 名稱區分大小寫。
> * 在跨叢集查詢中，請確定 Application Insights apps 與 Log Analytics 工作區的命名正確。
> * 如果名稱包含特殊字元，則會以 proxy 叢集名稱中的 URL 編碼來取代它們。 
> * 如果名稱包含的字元不符合 [KQL 識別碼名稱規則](kusto/query/schema-entities/entity-names.md)，則會以虛線字元取代這些字元 **-** 。

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>從 LA 或 AI ADX Proxy 叢集進行直接查詢

在您的 LA 或 AI 叢集上執行查詢。 確認已在左窗格中選取您的叢集。 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![查詢 LA 工作區](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>對 LA 或 AI ADX Proxy 叢集和 ADX 原生叢集的交叉查詢

當您從 proxy 執行跨叢集查詢時，請確認已在左窗格中選取您的 ADX 原生叢集。 下列範例示範如何使用 `union`) 搭配 LA 工作區 (，結合 ADX 叢集資料表。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![從 Azure 資料總管 proxy 進行交叉查詢](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

使用[ `join` 運算子](kusto/query/joinoperator.md)（而非 union）可能需要在 [`hint`](kusto/query/joinoperator.md#join-hints) Azure 資料總管原生叢集 (上執行，而不是在 proxy) 上執行。 

### <a name="join-data-from-an-adx-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>使用另一個租使用者中的 Azure 監視器資源，從一個租使用者中的 ADX 叢集中聯結資料

ADX Proxy 不支援跨租使用者查詢。 您已登入單一租使用者，以執行橫跨這兩個資源的查詢。

如果 Azure 資料總管資源位於租使用者 ' A '，而 LA 工作區位於租使用者 ' B ' 中，請使用下列兩種方法之一：

1. Azure 資料總管可讓您為不同租使用者中的主體新增角色。 在租使用者 ' B ' 中新增您的使用者識別碼，作為 Azure 資料總管叢集上的授權使用者。 驗證 Azure 資料總管叢集中包含租使用者 ' B ' 的 *' ExternalTrustedTenant '* 屬性。 在租使用者 ' B ' 中完整執行交叉查詢。 

2. 使用 [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) 將 Azure 監視器資源投影至租使用者 ' A '。

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>從不同的租使用者連接到 Azure 資料總管叢集

Kusto Explorer 會自動將您登入使用者帳戶原本所屬的租使用者。 若要使用相同的使用者帳戶存取其他租使用者中的資源，必須 `tenantId` 在連接字串中明確指定： `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=\*\*TenantId**`

## <a name="function-supportability"></a>函數支援能力

Azure 資料總管 proxy 叢集支援 Application Insights 和 Log Analytics 的功能。
這項功能可讓跨叢集查詢直接參考 Azure 監視器的表格式函數。
Proxy 支援下列命令：

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

下圖描述從 Azure 資料總管 Web UI 查詢表格式函數的範例。
若要使用函數，請在查詢視窗中執行名稱。

  [![從 Azure 資料總管 WEB UI 查詢表格式函數](media/adx-proxy/function-query-adx-proxy.png)](media/adx-proxy/function-query-adx-proxy.png#lightbox)


## <a name="additional-syntax-examples"></a>其他語法範例

呼叫 Application Insights (AI) 或 Log Analytics (LA) 叢集時，可以使用下列語法選項：

|語法描述  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 叢集內的資料庫僅包含此訂用帳戶中已定義的資源 (**建議用於跨叢集查詢**)  |   叢集 (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`)  | 叢集 (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)      |
| 包含此訂用帳戶中所有應用程式/工作區的叢集    |     叢集 (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)     |    叢集 (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)      |
|包含訂用帳戶中所有應用程式/工作區的叢集，且為此資源群組的成員    |   叢集 (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)       |    叢集 (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)       |
|僅包含此訂用帳戶中已定義資源的叢集      |    叢集 (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)     |  叢集 (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)      |

## <a name="next-steps"></a>後續步驟

[撰寫查詢](write-queries.md)
