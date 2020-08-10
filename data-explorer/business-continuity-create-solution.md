---
title: 使用 Azure 資料總管建立商務持續性和嚴重損壞修復解決方案
description: 本文說明如何使用 Azure 資料總管來建立商務持續性和嚴重損壞修復解決方案
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 0b37d65d095806d108a632c315820d164f45a520
ms.sourcegitcommit: 39a055e246539ac64d651abb42531892dd4393e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88028888"
---
# <a name="create-business-continuity-and-disaster-recovery-solutions-with-azure-data-explorer"></a>使用 Azure 資料總管建立商務持續性和嚴重損壞修復解決方案

本文會詳細說明如何在不同的 Azure 區域中複寫 Azure 資料總管資源、管理和內嵌，以準備 Azure 區域中斷。 提供使用事件中樞的資料內嵌範例。 此外，也會針對不同的架構設定討論成本優化。 如需架構考慮和復原解決方案的深入探討，請參閱[商務持續性總覽](business-continuity-overview.md)。

## <a name="prepare-for-azure-regional-outage-to-protect-your-data"></a>準備 Azure 區域中斷以保護您的資料

Azure 資料總管不支援自動保護整個 Azure 區域中斷的情況。 這種中斷情形可能發生在自然災害之類的自然災難。 如果您需要解決嚴重損壞修復狀況的問題，請執行下列步驟以確保商務持續性。 在這些步驟中，您會在兩個 Azure 配對的區域中複寫您的叢集、管理和資料內嵌。

1. 在兩個 Azure 配對的區域中[建立兩個或多個獨立](#create-multiple-independent-clusters)叢集。
1. 複寫[所有管理活動](#replicate-management-activities)，例如建立新的資料表或管理每個叢集上的使用者角色。
1. 以平行方式將資料內嵌到每個叢集。

### <a name="create-multiple-independent-clusters"></a>建立多個獨立叢集

在一個以上的區域中建立一個以上的[Azure 資料總管](create-cluster-database-portal.md)叢集。
請確定在[Azure 配對的區域](/azure/best-practices-availability-paired-regions)中至少會建立兩個叢集。

下圖顯示覆本、三個不同區域中的三個叢集。 

:::image type="content" source="media/business-continuity-create-solution/independent-clusters.png" alt-text="建立獨立叢集":::

### <a name="replicate-management-activities"></a>複寫管理活動

在每個複本中複寫管理活動，使其具有相同的叢集設定。

1. 在每個複本上建立相同的： 
    * 資料庫：您可以使用[Azure 入口網站](create-cluster-database-portal.md#create-a-database)或我們的其中一個[sdk](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/kusto/Microsoft.Azure.Management.Kusto)來建立新的資料庫。
    * [資料表](kusto/management/create-table-command.md)
    * [對應](kusto/management/create-ingestion-mapping-command.md)
    * [原則](kusto/management/policies.md)

1. 管理每個複本上的[驗證和授權](kusto/management/security-roles.md)。

    :::image type="content" source="media/business-continuity-create-solution/regional-duplicate-management.png" alt-text="重複的管理活動":::    

### <a name="configure-data-ingestion"></a>設定資料內嵌

在每個叢集上一致地設定資料內嵌。 下列內嵌方法會使用下列先進的商務持續性功能。

|內嵌方法  |嚴重損壞修復功能  |
|---------|---------|
|[Iot 中樞](/azure/iot-hub/iot-hub-ha-dr#cross-region-dr)  |[Microsoft 起始的容錯移轉和手動容錯移轉](/azure/iot-hub/iot-hub-ha-dr#cross-region-dr) |
|[事件中樞](ingest-data-event-hub.md) | 使用[主要和次要嚴重損壞修復命名空間的](/azure/event-hubs/event-hubs-geo-dr)中繼資料災難復原     |
|[使用事件方格訂用帳戶從儲存體內嵌](ingest-data-event-grid.md) |  針對傳送至事件中樞和嚴重損壞[修復和帳戶容錯移轉策略](/azure/storage/common/storage-disaster-recovery-guidance)的 blob 建立訊息，執行異地嚴重損壞[修復](/azure/event-hubs/event-hubs-geo-dr)       |

## <a name="disaster-recovery-solution-using-event-hub-ingestion"></a>使用事件中樞內嵌的嚴重損壞修復解決方案

當您完成[準備 Azure 區域中斷以保護您的資料](#prepare-for-azure-regional-outage-to-protect-your-data)之後，您的資料和管理會散發到多個區域。 如果某個區域發生中斷，Azure 資料總管將能夠使用其他複本。 

### <a name="set-up-ingestion-using-event-hub"></a>使用事件中樞設定內嵌

下列範例會使用透過事件中樞的內嵌。 已設定[容錯移轉流程](/azure/event-hubs/event-hubs-geo-dr#setup-and-failover-flow)，且 Azure 資料總管從別名內嵌資料。 針對每個叢集複本使用唯一的取用者群組，[從事件中樞內嵌資料](ingest-data-event-hub.md)。 否則，您將會散佈流量，而不是進行複寫。

> [!NOTE] 
> 透過事件中樞/IoT 中樞/儲存體的內嵌功能非常強大。 如果叢集無法使用一段時間，它會在稍後攔截，並插入任何暫止的訊息或 blob。 此程式依賴[檢查點](/azure/event-hubs/event-hubs-features#checkpointing)。

:::image type="content" source="media/business-continuity-create-solution/event-hub-management-scheme.png" alt-text="透過事件中樞內嵌":::

如下圖所示，您的資料來源會產生事件到容錯移轉設定的事件中樞，而每個 Azure 資料總管複本都會取用這些事件。 Power BI、Grafana 或 SDK 驅動 WebApps 之類的資料視覺效果元件可以查詢其中一個複本。

:::image type="content" source="media/business-continuity-create-solution/data-sources-visualization.png" alt-text="資料來源至資料視覺效果":::

## <a name="optimize-costs"></a>將成本優化

現在您已經準備好使用下列一些方法來優化您的複本：

* [建立主動-熱待命設定](#create-an-active-hot-standby-configuration)
* [啟動和停止複本](#start-and-stop-the-replicas)
* [執行高可用性應用程式服務](#implement-a-highly-available-application-service)
* [將主動-主動設定中的成本優化](#optimize-cost-in-an-active-active-configuration)

### <a name="create-an-active-hot-standby-configuration"></a>建立主動-熱待命設定

複寫和更新 Azure 資料總管安裝程式會以線性方式增加複本數目的成本。 若要將成本優化，您可以執行架構變異來平衡時間、容錯移轉和成本。
在主動-熱待命設定中，已藉由引進被動 Azure 資料總管複本來實行成本優化。 只有在主要區域發生嚴重損壞時，才會開啟這些複本 (例如，區域 A) 。 區域 B 和 C 中的複本不需要處於使用中的24/7，因此可大幅降低成本。 不過，在大多數情況下，這些複本的效能將不會與主要叢集一樣好。 如需詳細資訊，請參閱[主動-熱待命](business-continuity-overview.md#active-hot-standby-configuration)設定。

在下圖中，只有一個叢集會從事件中樞內嵌資料。 區域 A 中的主要叢集會執行所有資料的[連續資料匯出](kusto/management/data-export/continuous-data-export.md)至儲存體帳戶。 次要複本可以使用[外部資料表](kusto/query/schema-entities/externaltables.md)存取資料。

:::image type="content" source="media/business-continuity-create-solution/active-hot-standby-scheme.png" alt-text="主動/熱待命的架構":::

### <a name="start-and-stop-the-replicas"></a>啟動和停止複本 

您可以使用下列其中一種方法來啟動和停止次要複本：

* [Azure 資料總管連接器以自動 (預覽) ](flow.md)

* Azure 入口網站中 [**總覽**]**索引標籤**上的 [停止] 按鈕。 如需詳細資訊，請參閱[停止並重新啟動](create-cluster-database-portal.md#stop-and-restart-the-cluster)叢集。

* Azure CLI： 

```kusto
az kusto cluster stop --name=<clusterName> --resource-group=<rgName> --subscription=<subscriptionId>” 
```

### <a name="implement-a-highly-available-application-service"></a>執行高可用性應用程式服務

#### <a name="create-the-azure-app-service-bcdr-client"></a>建立 Azure App Service BCDR 用戶端

本節說明如何建立支援單一主要和多個次要 Azure 資料總管叢集連接的[Azure App Service](https://azure.microsoft.com/services/app-service/) 。 下圖說明 Azure App Service 設定。

:::image type="content" source="media/business-continuity-create-solution/app-service-setup.png" alt-text="建立 Azure App Service":::

> [!TIP]
> 在相同服務中的複本之間擁有多個連接，可提供更高的可用性。 此設定在區域性中斷的情況下並不適用。  

1. [針對 app service 使用此未定案程式碼](https://github.com/Azure/azure-kusto-bcdr-boilerplate)。 若要執行多叢集用戶端，已建立[AdxBcdrClient](https://github.com/Azure/azure-kusto-bcdr-boilerplate/blob/master/webapp/ADX/AdxBcdrClient.cs)類別。 使用此用戶端執行的每個查詢都會[先傳送至主要](https://github.com/Azure/azure-kusto-bcdr-boilerplate/blob/26f8c092982cb8a3757761217627c0e94928ee07/webapp/ADX/AdxBcdrClient.cs#L69)叢集。 如果發生失敗，查詢將會傳送至次要複本。

1. 使用[自訂 application insights 計量](/azure/azure-monitor/app/api-custom-events-metrics)來測量效能，並要求散發至主要和次要叢集。 

#### <a name="test-the-azure-app-service-bcdr-client"></a>測試 Azure App Service BCDR 用戶端

我們使用多個 Azure 資料總管複本來執行測試。 在主要和次要叢集模擬中斷之後，您可以看到 app service BCDR 用戶端的行為如預期。

:::image type="content" source="media/business-continuity-create-solution/simulation-verify-service.png" alt-text="確認 app service BCDR 用戶端":::

Azure 資料總管叢集會散佈于西歐 (2xD14v2 主要) 、南部東亞和美國東部 (2xD11v2) 。 

:::image type="content" source="media/business-continuity-create-solution/performance-test-query-time.png" alt-text="跨地球查詢回應時間":::

> [!NOTE]
> 較慢的回應時間是由於不同的 Sku 和跨地球查詢所造成。

#### <a name="perform-dynamic-or-static-routing"></a>執行動態或靜態路由

使用[Azure 流量管理員路由方法](/azure/traffic-manager/traffic-manager-routing-methods)來進行要求的動態或靜態路由。  Azure 流量管理員是以 DNS 為基礎的流量負載平衡器，可讓您散發 app service 流量。 此流量已針對跨全域 Azure 區域的服務優化，同時提供高可用性和回應性。 

您也可以使用[Azure Front 門板路由](/azure/frontdoor/front-door-routing-methods)。 如需這兩種方法的比較，請參閱[使用 Azure 的應用程式傳遞套件進行負載平衡](/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite)。

### <a name="optimize-cost-in-an-active-active-configuration"></a>將主動-主動設定中的成本優化

使用主動-主動設定進行嚴重損壞修復時，會以線性方式增加成本。 成本包括節點、儲存體、標記，以及[頻寬](https://azure.microsoft.com/pricing/details/bandwidth/)增加的網路成本。

#### <a name="use-optimized-autoscale-to-optimize-costs"></a>使用優化的自動調整來優化成本

使用[優化的自動](manage-cluster-horizontal-scaling.md#optimized-autoscale)調整功能來設定次要叢集的水準縮放。 您應該將其維度為可處理內嵌負載。 一旦無法連線到主要叢集，次要叢集將會取得更多的流量，並根據設定進行調整。 

在此範例中使用優化自動調整，相較于在所有複本上具有相同的水準和垂直縮放比例，已節省大約50% 的成本。

## <a name="next-steps"></a>後續步驟

開始使用[商務持續性和嚴重損壞修復總覽](business-continuity-overview.md)。
