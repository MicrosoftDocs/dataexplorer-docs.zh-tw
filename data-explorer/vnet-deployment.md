---
title: 將 Azure 資料資源管理員部署到虛擬網路
description: 瞭解如何將 Azure 資料資源管理員部署到虛擬網路
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 170db0d00f42209a2fd55c4009ab2f11eff55641
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610197"
---
# <a name="deploy-azure-data-explorer-cluster-into-your-virtual-network"></a>將 Azure 資料資源管理器叢集部署到虛擬網路

本文介紹將 Azure 資料資源管理器群集部署到自定義 Azure 虛擬網路時存在的資源。 此資訊將説明您將群集部署到虛擬網路 (VNet) 中的子網中。 有關 Azure 虛擬網路的詳細資訊,請參閱[什麼是 Azure 虛擬網路?](/azure/virtual-network/virtual-networks-overview)

   ![vnet 圖](media/vnet-deployment/vnet-diagram.png)

Azure 資料資源管理員支援將群集部署到虛擬網路 (VNet) 中的子網。 此功能使您能夠:

* 在 Azure 資料資源管理器群集流量上強制實施[網路安全組](/azure/virtual-network/security-overview)(NSG) 規則。
* 將本地網路連接到 Azure 資料資源管理器群集的子網。
* 使用[服務終結點](/azure/virtual-network/virtual-network-service-endpoints-overview)保護資料連接來源([事件中心](/azure/event-hubs/event-hubs-about)和[事件網格](/azure/event-grid/overview))。

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>在 VNet 中存取 Azure 資料資源管理員群集

您可以使用以下 IP 位址存取 Azure 資料資源管理員群集,每個服務(引擎和資料管理服務):

* **專用 IP**:用於造訪 VNet 內的群集。
* **公共 IP**:用於從 VNet 外部存取群集以進行管理和監視,並作為出站連接的源位址從群集啟動。

建立以下 DNS 紀錄以存取服務: 

* `[clustername].[geo-region].kusto.windows.net`(發動機)`ingest-[clustername].[geo-region].kusto.windows.net` (資料管理)映射到每個服務的公共 IP。 

* `private-[clustername].[geo-region].kusto.windows.net`(發動機)`private-ingest-[clustername].[geo-region].kusto.windows.net` (數據管理)映射到每個服務的專用 IP。

## <a name="plan-subnet-size-in-your-vnet"></a>在 VNet 中規劃子網大小

部署子網後,無法更改用於承載 Azure 資料資源管理器群集的子網的大小。 在 VNet 中,Azure 資料資源管理員為每個 VM 使用一個專用 IP 位址,對內部負載均衡器(引擎和資料管理)使用兩個專用 IP 位址。 Azure 網路還為每個子網使用五個 IP 位址。 Azure 資料資源管理員為數據管理服務提供了兩個 VM。 引擎服務 VM 按使用者配置規模容量進行預配。

IP 位址總數:

| 使用 | 位址數目 |
| --- | --- |
| 發動機服務 | 每個實體 1 個 |
| 資料管理服務 | 2 |
| 內部負載平衡器 | 2 |
| Azure 保留位址 | 5 |
| **總** | **#engine_instances = 9** |

> [!IMPORTANT]
> 子網大小必須提前計劃,因為部署 Azure 數據資源管理器後無法更改子網大小。 因此,相應地保留所需的子網大小。

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>連接到 Azure 資料資源管理員的服務終結點

[Azure 服務終結點](/azure/virtual-network/virtual-network-service-endpoints-overview)使您能夠保護 Azure 多租戶資源到虛擬網路。
將 Azure 資料資源管理器群集部署到子網允許您設置與[事件中心](/azure/event-hubs/event-hubs-about)或[事件網格](/azure/event-grid/overview)的數據連接,同時限制 Azure 資料資源管理器子網的基礎資源。

> [!NOTE]
> 將事件Grid設定與[儲存](/azure/storage/common/storage-introduction)和 [事件中心] 一起使用時,訂閱中使用的儲存帳戶可以與 Azure 資料資源管理器子網的服務終結點一起鎖定,同時允許在[防火牆配置](/azure/storage/common/storage-network-security)中使用受信任的 Azure 平台服務,但事件中心無法啟用終結點,因為它不支援受信任的 Azure[平台服務](/azure/event-hubs/event-hubs-service-endpoints)。

## <a name="dependencies-for-vnet-deployment"></a>VNet 部署的相依項

### <a name="network-security-groups-configuration"></a>網路安全組設定

[網路安全組 (NSG)](/azure/virtual-network/security-overview)提供控制 VNet 內網路訪問的能力。 可以使用兩個終結點訪問 Azure 數據資源管理員:HTT(443)和 TDS (1433)。 必須配置以下 NSG 規則,以允許訪問這些終結點,以便管理、監視和正確操作群集。

#### <a name="inbound-nsg-configuration"></a>置站 NSG 設定

| **使用**   | **從**   | **至**   | **通訊協定**   |
| --- | --- | --- | --- |
| 管理性  |[ADX 管理位址](#azure-data-explorer-management-ip-addresses)/Azure 資料資源管理員管理(服務標籤) | ADX 子網:443  | TCP  |
| 健康狀況監視  | [ADX 執行狀況監控位址](#health-monitoring-addresses)  | ADX 子網:443  | TCP  |
| ADX 內部通訊  | ADX 子網:所有連接埠  | ADX 子網:所有連接埠  | 全部  |
| 允許 Azure 負載均衡器入站(執行狀況探測)  | AzureLoadBalancer  | ADX 子網:80,443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>出站 NSG 設定

| **使用**   | **從**   | **至**   | **通訊協定**   |
| --- | --- | --- | --- |
| 與 Azure 儲存體的相依性  | ADX 子網  | 存儲:443  | TCP  |
| 對 Azure 資料湖的依賴  | ADX 子網  | AzureDataLake:443  | TCP  |
| 事件中心引入和服務監視  | ADX 子網  | 活動中心:443,5671  | TCP  |
| 發佈指標  | ADX 子網  | Azure 監視器:443 | TCP  |
| Azure 監視器設定下載  | ADX 子網  | [Azure 監視器設定終結點位址](#azure-monitor-configuration-endpoint-addresses):443 | TCP  |
| 動作目錄(如果適用) | ADX 子網 | Azure 活動目錄:443 | TCP |
| 憑證授權單位 | ADX 子網 | 互聯網:80 | TCP |
| 內部通訊  | ADX 子網  | ADX 子網:所有連接埠  | 全部  |
| 與`sql\_request``http\_request`外掛程式的連接埠  | ADX 子網  | 互聯網:自訂  | TCP  |

### <a name="relevant-ip-addresses"></a>相關 IP 位址

#### <a name="azure-data-explorer-management-ip-addresses"></a>Azure 資料資源管理員管理 IP 位址

| 區域 | 位址 |
| --- | --- |
| 澳大利亞中部 | 20.37.26.134 |
| 澳大利亞中部2 | 20.39.99.177 |
| 澳大利亞東部 | 40.82.217.84 |
| 澳大利亞東南部 | 20.40.161.39 |
| 巴西南部 | 191.233.25.183 |
| 加拿大中部 | 40.82.188.208 |
| 加拿大東部 | 40.80.255.12 |
| 印度中部 | 40.81.249.251, 104.211.98.159 |
| 美國中部 | 40.67.188.68 |
| 美國中部 EUAP | 40.89.56.69 |
| 東亞 | 20.189.74.103 |
| 美國東部 | 52.224.146.56 |
| 美國東部 2 | 52.232.230.201 |
| 東 US2 EUAP | 52.253.226.110 |
| 法國中部 | 40.66.57.91 |
| 法國南部 | 40.82.236.24 |
| 日本東部 | 20.43.89.90 |
| 日本西部 | 40.81.184.86 |
| 南韓中部 | 40.82.156.149 |
| 南韓南部 | 40.80.234.9 |
| 美國中北部 | 40.81.45.254 |
| 北歐 | 52.142.91.221 |
| 南非北部 | 102.133.129.138 |
| 南非西部 | 102.133.0.97 |
| 美國中南部 | 20.45.3.60 |
| 東南亞 | 40.119.203.252 |
| 印度南部 | 40.81.72.110, 104.211.224.189 |
| 英國南部 | 40.81.154.254 |
| 英國西部 | 40.81.122.39 |
| 美國中西部 | 52.159.55.120 |
| 西歐 | 51.145.176.215 |
| 印度西部 | 40.81.88.112, 104.211.160.120 |
| 美國西部 | 13.64.38.225 |
| 美國西部 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>執行狀況監測位址

| 區域 | 位址 |
| --- | --- |
| 澳大利亞中部 | 191.239.64.128 |
| 澳大利亞中部 2 | 191.239.64.128 |
| 澳大利亞東部 | 191.239.64.128 |
| 澳大利亞東南部 | 191.239.160.47 |
| 巴西南部 | 23.98.145.105 |
| 加拿大中部 | 168.61.212.201 |
| 加拿大東部 | 168.61.212.201 |
| 印度中部 | 23.99.5.162 |
| 美國中部 | 168.61.212.201 |
| 美國中部 EUAP | 168.61.212.201 |
| 東亞 | 168.63.212.33 |
| 美國東部 | 137.116.81.189 |
| 美國東部 2 | 137.116.81.189 |
| 美國東部 2 EUAP | 137.116.81.189 |
| 法國中部 | 23.97.212.5 |
| 法國南部 | 23.97.212.5 |
| 日本東部 | 138.91.19.129 |
| 日本西部 | 138.91.19.129 |
| 南韓中部 | 138.91.19.129 |
| 南韓南部 | 138.91.19.129 |
| 美國中北部 | 23.96.212.108 |
| 北歐 | 191.235.212.69 
| 南非北部 | 104.211.224.189 |
| 南非西部 | 104.211.224.189 |
| 美國中南部 | 23.98.145.105 |
| 印度南部 | 23.99.5.162 |
| 東南亞 | 168.63.173.234 |
| 英國南部 | 23.97.212.5 |
| 英國西部 | 23.97.212.5 |
| 美國中西部 | 168.61.212.201 |
| 西歐 | 23.97.212.5 |
| 印度西部 | 23.99.5.162 |
| 美國西部 | 23.99.5.162 |
| 美國西部 2 | 23.99.5.162, 104.210.32.14 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Azure 監視器設定終結點位址

| 區域 | 位址 |
| --- | --- |
| 澳大利亞中部 | 52.148.86.165 |
| 澳大利亞中部 2 | 52.148.86.165 |
| 澳大利亞東部 | 52.148.86.165 |
| 澳大利亞東南部 | 52.148.86.165 |
| 巴西南部 | 13.68.89.19 |
| 加拿大中部 | 13.90.43.231 |
| 加拿大東部 | 13.90.43.231 |
| 印度中部 | 13.71.25.187 |
| 美國中部 | 52.173.95.68 |
| 美國中部 EUAP | 13.90.43.231 |
| 東亞 | 13.75.117.221 |
| 美國東部 | 13.90.43.231 |
| 美國東部 2 | 13.68.89.19 |    
| 美國東部 2 EUAP | 13.68.89.19 |
| 法國中部 | 52.174.4.112 |
| 法國南部 | 52.174.4.112 |
| 日本東部 | 13.75.117.221 |
| 日本西部 | 13.75.117.221 |
| 南韓中部 | 13.75.117.221 |
| 南韓南部 | 13.75.117.221 |
| 美國中北部 | 52.162.240.236 |
| 北歐 | 52.169.237.246 |
| 南非北部 | 13.71.25.187 |
| 南非西部 | 13.71.25.187 |
| 美國中南部 | 13.84.173.99 |
| 印度南部 | 13.71.25.187 |
| 東南亞 | 52.148.86.165 |
| 英國南部 | 52.174.4.112 |
| 英國西部 | 52.169.237.246 |
| 美國中西部 | 52.161.31.69 |
| 西歐 | 52.174.4.112 |
| 印度西部 | 13.71.25.187 |
| 美國西部 | 40.78.70.148 |
| 美國西部 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>快速路由設定

使用 ExpressRoute 將本地網路連接到 Azure 虛擬網路。 常見設置是通過邊界網關協定 (BGP) 會話通告預設路由 (0.0.0.0/0)。 這迫使從虛擬網路流出的流量轉發到客戶的前提網路,這可能會丟棄流量,從而導致出站流中斷。 為了克服此預設值,可以設定[使用者定義路由 (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0),下一個躍點將是*Internet*。 由於UDR優先於BGP,因此流量將發送到Internet。

## <a name="securing-outbound-traffic-with-firewall"></a>使用防火牆保護出站流量

如果要使用[Azure 防火牆](/azure/firewall/overview)或任何虛擬設備保護出站流量以限制網域名稱,則必須在防火牆中允許以下完全限定功能變數名稱 (FQDN)。

```
prod.warmpath.msftcloudes.com:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443
shoebox2.metrics.nsatc.net:443
prod-dsts.dsts.core.windows.net:443
*.identity.azure.net:443
*.vault.azure.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

您還需要使用下一躍點*Internet*使用[管理位址](#azure-data-explorer-management-ip-addresses)和[運行狀況監視位址](#health-monitoring-addresses)在子網上定義[路由表](/azure/virtual-network/virtual-networks-udr-overview),以防止出現非對稱路由問題。

例如,對於**美國西部**區域,必須定義以下 UDR:

| 名稱 | 位址首碼 | 下一跳 |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>使用 Azure 資源管理員樣本將 Azure 資料資源管理員叢集部署到 VNet

要將 Azure 資料資源管理器群集部署到虛擬網路,請使用[「將 Azure 資料資源管理器」叢集部署到 VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure 資源管理器樣本中。

此範本創建群集、虛擬網路、子網、網路安全組和公共 IP 位址。
