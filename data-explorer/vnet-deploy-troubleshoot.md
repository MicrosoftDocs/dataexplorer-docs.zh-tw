---
title: 針對您的虛擬網路中的 Azure 資料總管叢集存取、內嵌和操作進行疑難排解
description: 針對您的虛擬網路中的 Azure 資料總管叢集進行連線、內嵌、叢集建立和操作進行疑難排解
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 49041ec72439d8f36b54ece5fcd341fa4ca873fc
ms.sourcegitcommit: bcb87ed043aca7c322792c3a03ba0508026136b4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86127326"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>針對您的虛擬網路中的 Azure 資料總管叢集存取、內嵌和操作進行疑難排解

在本節中，您將瞭解如何針對部署到[虛擬網路](/azure/virtual-network/virtual-networks-overview)的叢集，針對連線、操作和叢集建立問題進行疑難排解。

## <a name="access-issues"></a>存取問題

如果您在使用公用（cluster.region.kusto.windows.net）或私用（private-cluster.region.kusto.windows.net）端點存取叢集時發生問題，而且懷疑它與虛擬網路設定相關，請執行下列步驟來針對問題進行疑難排解。

### <a name="check-tcp-connectivity"></a>檢查 TCP 連線能力

第一個步驟包括使用 Windows 或 Linux OS 檢查 TCP 連線能力。

# <a name="windows"></a>[Windows](#tab/windows)

   1. 將[TCping](https://www.elifulkerson.com/projects/tcping.php)下載到連接到叢集的電腦。
   1. 使用下列命令，從來源機器 Ping 目的地：

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

   1. 使用下列命令，從來源機器 Ping 目的地：

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

如果測試不成功，請繼續進行下列步驟。 如果測試成功，問題不是因為 TCP 連線問題所造成。 請移至[操作問題](#cluster-creation-and-operations-issues)以進一步進行疑難排解。

### <a name="check-the-network-security-group-nsg"></a>檢查網路安全性群組（NSG）

   檢查連接到叢集子網的[網路安全性群組](/azure/virtual-network/security-overview)（NSG）是否有輸入規則，允許從用戶端電腦的 IP 存取埠443。

### <a name="check-route-table"></a>檢查路由表

   如果叢集的子網已將強制通道設定設為防火牆（子網具有包含預設路由 ' 0.0.0.0/0 ' 的[路由表](/azure/virtual-network/virtual-networks-udr-overview)），請確定電腦 IP 位址具有[下一個躍點類型](/azure/virtual-network/virtual-networks-udr-overview)為 VirtualNetwork/Internet 的路由。 需要此路由才能避免非對稱式路由問題。

## <a name="ingestion-issues"></a>內嵌問題

如果您遇到內嵌問題，而且懷疑它與虛擬網路設定相關，請執行下列步驟。

### <a name="check-ingestion-health"></a>檢查內嵌健全狀況

檢查叢集內嵌[計量](using-metrics.md#ingestion-health-and-performance-metrics)是否表示狀況良好狀態。

### <a name="check-security-rules-on-data-source-resources"></a>檢查資料來源資源的安全性規則

如果計量指出沒有從資料來源處理的事件（事件/IoT 中樞的*事件已處理*計量），請確定資料來源資源（事件中樞或儲存體）允許從防火牆規則或服務端點中的叢集子網進行存取。

### <a name="check-security-rules-configured-on-clusters-subnet"></a>檢查叢集子網上設定的安全性規則

請確定叢集的子網已正確設定 NSG、UDR 和防火牆規則。 此外，也請測試所有相依端點的網路連線能力。 

## <a name="cluster-creation-and-operations-issues"></a>叢集建立和作業問題

如果您遇到叢集建立或操作問題，而且懷疑它與虛擬網路設定相關，請遵循下列步驟來針對問題進行疑難排解。

### <a name="check-the-dns-servers-configuration"></a>檢查「DNS 伺服器」設定

不支援自訂 DNS 伺服器。 在虛擬網路的 [ **DNS 伺服器**設定] 區段中，使用預設選項。

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

1. 等候操作完成

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
    
   等到 [*狀態*] 屬性顯示為 [*已完成*]，然後 [*屬性*] 欄位應該會顯示：

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

如果 [*結果*] 屬性顯示空的結果，表示所有的網路測試都已通過，而且沒有任何連線中斷。 如果顯示下列錯誤，輸出相依性 *' {dependencyName}： {埠} ' 可能不滿足（輸出）*，叢集就無法連線到依存的服務端點。 繼續進行下列步驟。

### <a name="check-network-security-group-nsg"></a>檢查網路安全性群組（NSG）

請根據[VNet 部署](vnet-deployment.md#dependencies-for-vnet-deployment)的相依性中的指示，確定已正確設定[網路安全性群組](/azure/virtual-network/security-overview)

### <a name="check-route-table"></a>檢查路由表

如果叢集的子網已將強制通道設定為防火牆（具有包含預設路由 ' 0.0.0.0/0 ' 之[路由表](/azure/virtual-network/virtual-networks-udr-overview)的子網），請確定[管理 ip 位址](vnet-deployment.md#azure-data-explorer-management-ip-addresses)和[健全狀況監視 ip 位址](vnet-deployment.md#health-monitoring-addresses)具有[下一個躍點類型](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools)為*網際網路*的路由，且[來源位址首碼](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route)為「*管理-ip/32* 」和「*健全狀況監視-ip/32*」。 此為避免非對稱式路由問題所需的路由。

### <a name="check-firewall-rules"></a>檢查防火牆規則

如果您強制將通道子網輸出流量傳送到防火牆，請確定防火牆設定中允許所有相依性 FQDN （例如， *blob.core.windows.net*），如[使用防火牆保護輸出流量](vnet-deployment.md#securing-outbound-traffic-with-firewall)中所述。
