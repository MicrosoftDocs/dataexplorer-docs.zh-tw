---
title: 使用 Kibana 將 Azure 資料總管中的資料視覺化
description: 在本文中，您將瞭解如何將 Azure 資料總管設定為 Kibana 的資料來源
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/12/2020
ms.openlocfilehash: 0d6695ddf6923dcbf44ac3466a2388edc7618551
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874965"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>使用 K2Bridge 開放原始碼連接器，將 Kibana 中 Azure 資料總管的資料視覺化

K2Bridge (Kibana-Kusto Bridge) 可讓您使用 Azure 資料總管作為資料來源，並將 Kibana 中的資料視覺化。 K2Bridge 是 [開放原始](https://github.com/microsoft/K2Bridge)碼的容器化應用程式。 它會作為 Kibana 實例與 Azure 資料總管叢集之間的 proxy。 本文說明如何使用 K2Bridge 來建立該連接。

K2Bridge 會將 Kibana 查詢轉譯為 Kusto 查詢語言 (KQL) ，並將 Azure 資料總管結果傳回 Kibana。

   ![透過 K2Bridge 與 Azure 資料總管的 Kibana 連接](media/k2bridge/k2bridge-chart.png)

K2Bridge 支援 Kibana 的 [ **探索** ] 索引標籤，您可以在其中：

* 搜尋及探索資料。
* 篩選結果。
* 在結果方格中新增或移除欄位。
* 查看記錄內容。
* 儲存並共用搜尋。

下圖顯示由 K2Bridge 系結至 Azure 資料總管的 Kibana 實例。 Kibana 中的使用者體驗不會改變。

   [![系結至 Azure 資料總管的 Kibana 頁面](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>先決條件

您必須備妥下列各項，才能在 Kibana 中將 Azure 中的資料視覺化資料總管：

* [Helm v3](https://github.com/helm/helm#install)，也就是 Kubernetes 套件管理員。

* Azure Kubernetes Service (AKS) 叢集或任何其他 Kubernetes 叢集。 版本1.14 到1.16 已經過測試和驗證。 如果您需要 AKS 叢集，請參閱如何 [使用 Azure CLI](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) 或 [使用 AZURE 入口網站](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)來部署 AKS 叢集。

* [Azure 資料總管](create-cluster-database-portal.md)叢集，包括叢集的 URL 和資料庫名稱。

* Azure Active Directory (Azure AD) 的服務主體，以授權在 Azure 資料總管中查看資料，包括用戶端識別碼和用戶端密碼。

    我們建議具有檢視器許可權的服務主體，且不鼓勵您使用較高層級的許可權。 [為 Azure AD 服務主體設定叢集的 view 許可權](manage-database-permissions.md#manage-permissions-in-the-azure-portal)。

    如需 Azure AD 服務主體的詳細資訊，請參閱 [建立 Azure AD 的服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)。

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS 上執行 K2Bridge) 

根據預設，K2Bridge 的 Helm 圖會參考位於 Microsoft Container Registry (MCR) 中的公開可用映射。 MCR 不需要任何認證。

1. 下載所需的 Helm 圖。

1. 將 Elasticsearch 相依性新增至 Helm。 需要相依性，因為 K2Bridge 會使用較小的內部 Elasticsearch 實例。 實例服務中繼資料相關的要求，例如索引模式查詢和儲存的查詢。 此內部實例不會儲存任何商務資料。 您可以將實例視為實作為詳細資料。

    1. 若要將 Elasticsearch 相依性新增至 Helm，請執行下列命令：

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. 若要從 GitHub 存放庫的圖表目錄取得 K2Bridge 圖：

        1. 從 [GitHub](https://github.com/microsoft/K2Bridge)複製存放庫。
        1. 移至 K2Bridges 根存放庫目錄。
        1. 執行此命令：

            ```bash
            helm dependency update charts/k2bridge
            ```

1. 部署 K2Bridge。

    1. 將變數設定為您環境的正確值。

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. （選擇性）啟用 Application Insights 遙測。 如果您是第一次使用 Application Insights，請 [建立 Application Insights 資源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。 [將檢測金鑰複製](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) 到變數。

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a> 安裝 K2Bridge 圖。

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        在設定 [中，您](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md)可以找到一組完整的設定選項。

    1. <a name="install-kibana-service"></a> 先前命令的輸出會建議下一個要部署 Kibana 的 Helm 命令。 （選擇性）執行此命令：

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```

    1. 使用埠轉送來存取 localhost 上的 Kibana。

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```

    1. 前往，以連接到 Kibana http://127.0.0.1:5601 。

    1. 將 Kibana 公開給使用者。 有多種方法可以這麼做。 您使用的方法主要取決於您的使用案例。

        例如，您可以將服務公開為 Load Balancer 服務。 若要這樣做，請將 **--set 服務. type = LoadBalancer** 參數新增至 [舊版 Kibana Helm **install** 命令](#install-kibana-service)。

        然後執行此命令：

        ```bash
        kubectl get service -w -n k2bridge
        ```

        輸出看起來應該會像下面這樣：

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx    <pending>     5601:30128/TCP   4m24s
        ```

        然後，您可以使用所產生的外部 IP 值來顯示。 使用它來存取 Kibana，方法是開啟瀏覽器並前往 \<EXTERNAL-IP\> ：5601。

1. 設定索引模式以存取您的資料。

    在新的 Kibana 實例中：

    1. 開啟 Kibana。
    1. 流覽至 [ **管理**]。
    1. 選取 **索引模式**。
    1. 建立索引模式。 索引的名稱必須完全符合資料表名稱或函式名稱，而不含星號 (\*) 。 您可以從清單複製相關的行。

> [!Note]
> 若要在其他 Kubernetes 提供者上執行 K2Bridge，請將 Elasticsearch **storageClassName** 值變更為 yaml 值，以符合提供者所建議的值。

## <a name="visualize-data"></a>顯現資料

當 Azure 資料總管設定為 Kibana 的資料來源時，您可以使用 Kibana 來流覽資料。

1. 在 Kibana 中，選取最左邊的功能表上的 [ **探索** ] 索引標籤。

1. 從最左邊的下拉式清單方塊中，選取索引模式。 此模式會定義您想要探索的資料來源。 在此情況下，索引模式是 Azure 資料總管資料表。

   ![選取索引模式](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. 如果您的資料有時間篩選欄位，您可以指定時間範圍。 在 [ **探索** ] 頁面的右上方，選取時間篩選準則。 根據預設，頁面會顯示過去15分鐘的資料。

   ![選取時間篩選](media/k2bridge/k2bridge-time-filter.png)

1. 結果資料表會顯示前500筆記錄。 您可以展開檔，以 JSON 或資料表格式檢查其欄位資料。

   ![展開的記錄](media/k2bridge/k2bridge-expand-record.png)

1. 根據預設，結果資料表包含 **_source** 資料行。 如果時間欄位存在，也會包含 **時間** 資料行。 您可以在最左邊的窗格中，選取功能變數名稱旁的 [ **加入** ]，將特定資料行新增至結果資料表。

   ![已反白顯示 [新增] 按鈕的特定資料行](media/k2bridge/k2bridge-specific-columns.png)

1. 在查詢列中，您可以透過下列方式來搜尋資料：

    * 輸入搜尋詞彙。
    * 使用 Lucene 查詢語法。 例如：
        * 搜尋「錯誤」以尋找包含此值的所有記錄。
        * 搜尋 "status： 200" 以取得 status 值為200的所有記錄。
    * 使用邏輯運算子 **and**、 **OR**和 **NOT**。
    * 使用星號 (\*) 和問號 (？ ) 萬用字元。 例如，查詢 "destination_city： L *" 符合目的地城市值以 "L" 或 "l" 開頭的記錄。  (K2Bridge 不區分大小寫。 ) 

    ![執行查詢](media/k2bridge/k2bridge-run-query.png)

    > [!Tip]
    > 在 [搜尋](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)中，您可以找到更多搜尋規則和邏輯。

1. 若要篩選您的搜尋結果，請使用頁面最右邊窗格上的欄位清單。 您可以在 [欄位] 清單中看到：

    * 欄位的前五個值。
    * 包含欄位的記錄數目。
    * 包含每個值的記錄百分比。

    >[!Tip]
    > 使用放大鏡來尋找具有特定值的所有記錄。

    ![已反白顯示放大鏡的欄位清單](media/k2bridge/k2bridge-field-list.png)

    您也可以使用放大鏡來篩選結果，並且查看結果資料表中每一筆記錄的結果資料表格式視圖。

     ![已反白顯示放大鏡的表格清單](media/k2bridge/k2bridge-table-list.png)

1. 選取 [ **儲存** ] 或 [ **共用** ] 供搜尋之用。

     ![醒目提示按鈕，以儲存或共用搜尋](media/k2bridge/k2bridge-save-search.png)
