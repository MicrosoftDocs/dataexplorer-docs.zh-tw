---
title: 針對您的虛擬網路中的 Azure 資料總管叢集存取、內嵌和操作進行疑難排解
description: 針對虛擬網路中的 Azure 資料總管叢集的連線、內嵌、叢集建立和操作進行疑難排解
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/24/2020
ms.openlocfilehash: 6f3831580c998814d956b57a58acc8acd7269abb
ms.sourcegitcommit: f2f9cc0477938da87e0c2771c99d983ba8158789
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/07/2020
ms.locfileid: "89502427"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>針對您的虛擬網路中的 Azure 資料總管叢集存取、內嵌和操作進行疑難排解

在本節中，您將瞭解如何針對部署至 [虛擬網路](/azure/virtual-network/virtual-networks-overview)的叢集，進行連線、操作和叢集建立問題的疑難排解。

## <a name="access-issues"></a>存取問題

如果您在使用 public (cluster.region.kusto.windows.net) 或私用 (private-cluster.region.kusto.windows.net) 端點存取叢集時遇到問題，而且您懷疑它與虛擬網路設定相關，請執行下列步驟來針對問題進行疑難排解。

### <a name="check-tcp-connectivity"></a>檢查 TCP 連線能力

第一個步驟包括檢查使用 Windows 或 Linux OS 的 TCP 連線能力。

# <a name="windows"></a>[Windows](#tab/windows)

1. 將 [TCping](https://www.elifulkerson.com/projects/tcping.php) 下載到連接到叢集的電腦。
1. 使用下列命令，從來源電腦 Ping 目的地：

   ```cmd
   C:\> tcping -t yourcluster.kusto.windows.net 443 
   ** Pinging continuously.  Press control-c to stop **
   Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
   ```

# <a name="linux"></a>[Linux](#tab/linux)

1. 在連接到叢集的電腦上安裝*netcat*

   ```bash
   $ apt-get install netcat
   ```

1. 使用下列命令，從來源電腦 Ping 目的地：

   ```bash
   $ netcat -z -v yourcluster.kusto.windows.net 443
   Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
   ```
---

如果測試不成功，請繼續進行下列步驟。 如果測試成功，則問題不是因為 TCP 連線問題所致。 移至 [操作問題](#cluster-creation-and-operations-issues) 以進行進一步的疑難排解。

### <a name="check-the-network-security-group-nsg"></a>檢查網路安全性群組 (NSG) 

檢查連結至叢集子網的 [網路安全性群組](/azure/virtual-network/security-overview) (NSG) 是否有允許從用戶端電腦的 IP 存取埠443的輸入規則。

### <a name="check-route-table"></a>檢查路由表

如果叢集的子網具有強制通道設定至防火牆 (子網的 [路由表](/azure/virtual-network/virtual-networks-udr-overview) 包含預設路由 ' 0.0.0.0/0 ' ) ，請確定電腦 IP 位址具有 [下一個躍點類型](/azure/virtual-network/virtual-networks-udr-overview) 至 VirtualNetwork/網際網路的路由。 需要此路由以防止非對稱式路由問題。

## <a name="ingestion-issues"></a>內嵌問題

如果您遇到內嵌問題，且懷疑它與虛擬網路設定有關，請執行下列步驟。

### <a name="check-ingestion-health"></a>檢查內嵌健全狀況

確認叢集內嵌 [計量](using-metrics.md#ingestion-metrics) 指出狀況良好的狀態。

### <a name="check-security-rules-on-data-source-resources"></a>檢查資料來源資源的安全性規則

如果計量指出沒有任何事件已從資料來源處理 (事件/IoT 中樞) 的事件 *處理* 計量，請確定資料來源資源 (事件中樞或儲存體) 允許從防火牆規則或服務端點中的叢集子網進行存取。

### <a name="check-security-rules-configured-on-clusters-subnet"></a>檢查在叢集的子網上設定的安全性規則

請確定叢集的子網已正確設定 NSG、UDR 和防火牆規則。 此外，請測試所有相依端點的網路連線能力。 

## <a name="cluster-creation-and-operations-issues"></a>叢集建立和操作問題

如果您遇到叢集建立或操作問題，而且您懷疑它與虛擬網路設定有關，請遵循下列步驟來針對問題進行疑難排解。

### <a name="check-the-dns-servers-configuration"></a>檢查「DNS 伺服器」設定

不支援自訂 DNS 伺服器。 使用虛擬網路的 [ **DNS 伺服器** 設定] 區段中的預設選項。

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>使用 REST API 診斷虛擬網路

[ARMClient](https://chocolatey.org/packages/ARMClient)是用來使用 PowerShell 來呼叫 REST API。 

1. 使用 ARMClient 登入

   ```powerShell
   armclient login
   ```

1. 叫用診斷作業

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. 檢查回應

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. 等候作業完成

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   等候 [ *狀態* ] 屬性顯示 [ *已完成*]，然後 [ *屬性* ] 欄位應該會顯示：

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

如果 [ *結果* ] 屬性顯示空白的結果，表示所有的網路測試都已通過，而且沒有任何連接中斷。 如果顯示下列錯誤， *可能不符合輸出相依性 ' {dependencyName}： {port} ' (輸出) *，叢集無法連接相依的服務端點。 繼續進行下列步驟。

### <a name="check-network-security-group-nsg"></a>檢查網路安全性群組 (NSG) 

請確定[網路安全性群組](/azure/virtual-network/security-overview)已根據[VNet 部署](vnet-deployment.md#dependencies-for-vnet-deployment)的相依性中的指示正確設定

### <a name="check-route-table"></a>檢查路由表

如果叢集的子網已將強制通道設定為防火牆 (子網的[路由表](/azure/virtual-network/virtual-networks-udr-overview)包含預設路由 ' 0.0.0.0/0 ' ) 請確定[管理 ip 位址](vnet-deployment.md#azure-data-explorer-management-ip-addresses)) 和[健康情況監視 ip 位址](vnet-deployment.md#health-monitoring-addresses)具有[下一個躍點類型](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools)*網際網路*的路由，以及[來源位址前置](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route)詞為「*管理-ip/32* 」和「健康情況*監視-ip/32*」。 這是防止非對稱式路由問題所需的路由。

### <a name="check-firewall-rules"></a>檢查防火牆規則

如果您強制通道子網輸出流量傳送到防火牆，請確定所有相依性 FQDN 都 (例如，如[保護使用防火牆的輸出流量](vnet-deployment.md#securing-outbound-traffic-with-firewall)所述，防火牆設定中允許*blob.core.windows.net) 。*
