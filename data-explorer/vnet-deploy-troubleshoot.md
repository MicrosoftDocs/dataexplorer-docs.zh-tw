---
title: 在虛擬網路中對 Azure 資料資源管理器群集的存取、引入和操作進行故障排除
description: 在虛擬網路中對 Azure 資料資源管理器群集的連接、引入、叢集建立和操作進行故障排除
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: b50b971a3b1980ad35a1a939bdf25f1c9e6ac7ba
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500758"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>在虛擬網路中對 Azure 資料資源管理器群集的存取、引入和操作進行故障排除

在本節中,您將瞭解如何解決部署到[虛擬網路](/azure/virtual-network/virtual-networks-overview)中的群集的連接、操作和群集創建問題。

## <a name="access-issues"></a>存取問題

如果使用公共 (cluster.region.kusto.windows.net) 或專用 (private-cluster.region.kusto.windows.net) 終結點訪問群集時出現問題,並且懷疑該終結點與虛擬網路設置相關,則執行以下步驟以排除問題。

### <a name="check-tcp-connectivity"></a>檢查 TCP 連線

第一步包括使用 Windows 或 Linux 作業系統檢查 TCP 連接。

# <a name="windows"></a>[Windows](#tab/windows)

   1. 將[TCping](https://www.elifulkerson.com/projects/tcping.php)下載到連接到群集的電腦。
   2. 使用以下指令從來源電腦 ping 目標:

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. 在連接到叢集的機器中安裝*網貓*

    ```bash
    $ apt-get install netcat
     ```

   2. 使用以下指令從來源電腦 ping 目標:

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

如果測試不成功,請執行以下步驟。 如果測試成功,則問題不是由於 TCP 連接問題。 轉到[操作問題](#cluster-creation-and-operations-issues)以進一步排除故障。

### <a name="check-the-network-security-group-nsg"></a>檢查網路安全群組 (NSG)

   檢查連接到群集子網的[網路安全組](/azure/virtual-network/security-overview)(NSG) 具有允許從用戶端電腦的 IP 訪問埠 443 的入站規則。

### <a name="check-route-table"></a>檢查路由表

   如果群集的子網具有強制隧道設置到防火牆(包含預設路由"0.0.0.0/0"[的路由表](/azure/virtual-network/virtual-networks-udr-overview)的子網),請確保電腦 IP 位址具有[下一個躍點類型](/azure/virtual-network/virtual-networks-udr-overview)到 VirtualNetwork/Internet 的路由。 此路由是防止非對稱路由問題所必需的。

## <a name="ingestion-issues"></a>攝入問題

如果您遇到引入問題,並且懷疑它與虛擬網路設置有關,則執行以下步驟。

### <a name="check-ingestion-health"></a>檢查攝入健康

檢查[群集引入指標](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics)指示正常狀態。

### <a name="check-security-rules-on-data-source-resources"></a>檢查資料來源的安全規則

如果指標指示未從數據源處理任何事件(事件/IoT 中心*的事件處理*指標),請確保數據源資源(事件中心或存儲)允許在防火牆規則或服務終結點中訪問群集的子網。

### <a name="check-security-rules-configured-on-clusters-subnet"></a>檢查在群組中設定的安全規則

確保群集的子網已正確配置了 NSG、UDR 和防火牆規則。 此外,測試所有從屬終結點的網路連接。 

## <a name="cluster-creation-and-operations-issues"></a>叢集建立及操作問題

如果您遇到群集創建或操作問題,並且懷疑它與虛擬網路設置有關,請按照以下步驟解決問題。

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>使用 REST API 診斷虛擬網路

[ARMClient](https://chocolatey.org/packages/ARMClient)用於使用 PowerShell 呼叫 REST API。 

1. 使用 ARMClient 登入

   ```powerShell
   armclient login
   ```

1. 呼叫診斷操作

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

1. 等待動作完成

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
    
   等待*狀態*屬性顯示 *「已完成」*, 然後*屬性*欄位應顯示:

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

如果 *「結果」* 屬性顯示空結果,則表示所有網路測試都通過,並且沒有中斷任何連接。 如果顯示以下錯誤,*則出站依賴項"[依賴項名稱]:[埠]"可能未滿足(出站),* 群集無法訪問從屬服務終結點。 繼續以下步驟。

### <a name="check-network-security-group-nsg"></a>檢查網路安全群組 (NSG)

確保網路安全[群組](/azure/virtual-network/security-overview)根據[VNet 部署相依項](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)的說明正確設定

### <a name="check-route-table"></a>檢查路由表

如果群集的子網具有強制隧道設置為防火牆(包含預設路由"0.0.0.0/0"[路由表](/azure/virtual-network/virtual-networks-udr-overview)的子網)請確保[管理 IP 位址](vnet-deployment.md#azure-data-explorer-management-ip-addresses))和[運行狀況監視 IP 位址](vnet-deployment.md#health-monitoring-addresses)具有[具有下一躍點類型的](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *Internet*的路由,並且[源位址前綴](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route)為 *"管理-ip/32"* 和 *"運行狀況監視-ip/32"。* 此路由需要防止非對稱路由問題。

### <a name="check-firewall-rules"></a>檢查防火牆規則

如果強制隧道子網出站流量到防火牆,請確保所有依賴項 FQDN(例如 *.blob.core.windows.net*)都允許在防火牆配置中,如[使用防火牆保護出站流量](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall)時所述。
