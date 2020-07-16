---
title: 將 Azure 資料總管部署至您的虛擬網路
description: 瞭解如何將 Azure 資料總管部署至您的虛擬網路
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 0b831b236c2604ef7d87c77f6955e6b5eaf2aae1
ms.sourcegitcommit: 4ae1508bbaa8fe9642dcfc8618d77f009bc8ec9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2020
ms.locfileid: "86405482"
---
# <a name="deploy-azure-data-explorer-cluster-into-your-virtual-network"></a>將 Azure 資料總管叢集部署至您的虛擬網路

本文說明當您將 Azure 資料總管叢集部署至自訂 Azure 虛擬網路時，所存在的資源。 這項資訊可協助您將叢集部署到虛擬網路（VNet）中的子網。 如需 Azure 虛擬網路的詳細資訊，請參閱[什麼是 azure 虛擬網路？](/azure/virtual-network/virtual-networks-overview)

   ![vnet 圖表](media/vnet-deployment/vnet-diagram.png)

Azure 資料總管支援將叢集部署至您虛擬網路（VNet）中的子網。 這項功能可讓您：

* 強制執行 Azure 資料總管叢集流量的[網路安全性群組](/azure/virtual-network/security-overview)（NSG）規則。
* 將內部部署網路連線到 Azure 資料總管叢集的子網。
* 使用[服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview)來保護您的資料連線來源（[事件中樞](/azure/event-hubs/event-hubs-about)和[事件方格](/azure/event-grid/overview)）。

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>在您的 VNet 中存取您的 Azure 資料總管叢集

您可以使用每個服務（引擎和資料管理服務）的下列 IP 位址來存取 Azure 資料總管叢集：

* **私人 IP**：用來存取 VNet 內的叢集。
* **公用 IP**：用來從 VNet 外部存取叢集以進行管理和監視，以及做為從叢集啟動之輸出連線的來源位址。

系統會建立下列 DNS 記錄來存取服務： 

* `[clustername].[geo-region].kusto.windows.net`（引擎） `ingest-[clustername].[geo-region].kusto.windows.net`（資料管理）會對應到每個服務的公用 IP。 

* `private-[clustername].[geo-region].kusto.windows.net`（引擎） `private-ingest-[clustername].[geo-region].kusto.windows.net`（資料管理）會對應到每個服務的私人 IP。

## <a name="plan-subnet-size-in-your-vnet"></a>規劃 VNet 中的子網大小

部署子網之後，就無法改變用來裝載 Azure 資料總管叢集的子網大小。 在您的 VNet 中，Azure 資料總管會針對每個 VM 使用一個私人 IP 位址，並為內部負載平衡器（引擎和資料管理）使用兩個私人 IP 位址。 Azure 網路也會針對每個子網使用五個 IP 位址。 Azure 資料總管會布建兩個適用于資料管理服務的 Vm。 系統會根據使用者設定調整容量來布建引擎服務 Vm。

IP 位址總數：

| 用途 | 位址數目 |
| --- | --- |
| 引擎服務 | 每個實例1個 |
| 資料管理服務 | 2 |
| 內部負載平衡器 | 2 |
| Azure 保留的位址 | 5 |
| **總計** | **#engine_instances + 9** |

> [!IMPORTANT]
> 必須事先規劃子網大小，因為在部署 Azure 資料總管之後，就無法變更它。 因此，請據以保留所需的子網大小。

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>用來連接到 Azure 資料總管的服務端點

[Azure 服務端點](/azure/virtual-network/virtual-network-service-endpoints-overview)可讓您將 azure 多租使用者資源保護到您的虛擬網路。
將 Azure 資料總管叢集部署至您的子網，可讓您在限制 Azure 資料總管子網的基礎資源時，使用[事件中樞](/azure/event-hubs/event-hubs-about)或[事件方格](/azure/event-grid/overview)來設定資料連線。

> [!NOTE]
> 搭配使用 EventGrid 安裝與[儲存體](/azure/storage/common/storage-introduction)和 [事件中樞] 時，訂用帳戶中所使用的儲存體帳戶可以透過服務端點鎖定至 Azure 資料總管的子網，同時允許[防火牆](/azure/storage/common/storage-network-security)設定中的受信任 azure 平臺服務，但事件中樞無法啟用服務端點，因為它不支援受信任的[azure 平臺服務](/azure/event-hubs/event-hubs-service-endpoints)。

## <a name="dependencies-for-vnet-deployment"></a>VNet 部署的相依性

### <a name="network-security-groups-configuration"></a>網路安全性群組設定

[網路安全性群組（NSG）](/azure/virtual-network/security-overview)提供控制 VNet 內網路存取的能力。 您可以使用兩個端點來存取 Azure 資料總管： HTTPs （443）和 TDS （1433）。 下列 NSG 規則必須設定為允許存取這些端點，以進行叢集的管理、監視和適當操作。 其他規則則取決於您的安全性方針。

#### <a name="inbound-nsg-configuration"></a>輸入 NSG 設定

| **使用**   | **From**   | **若要**   | **通訊協定**   |
| --- | --- | --- | --- |
| 管理  |[ADX 管理位址](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement （ServiceTag） | ADX 子網：443  | TCP  |
| 健康狀況監視  | [ADX 健全狀況監視位址](#health-monitoring-addresses)  | ADX 子網：443  | TCP  |
| ADX 內部通訊  | ADX 子網：所有埠  | ADX 子網：所有埠  | 全部  |
| 允許 Azure 負載平衡器輸入（健康情況探查）  | AzureLoadBalancer  | ADX 子網：80443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>輸出 NSG 設定

| **使用**   | **From**   | **若要**   | **通訊協定**   |
| --- | --- | --- | --- |
| 與 Azure 儲存體的相依性  | ADX 子網  | 儲存體：443  | TCP  |
| Azure Data Lake 的相依性  | ADX 子網  | AzureDataLake：443  | TCP  |
| EventHub 內嵌和服務監視  | ADX 子網  | EventHub：443、5671  | TCP  |
| 發行計量  | ADX 子網  | AzureMonitor：443 | TCP  |
| Azure 監視器設定下載  | ADX 子網  | [Azure 監視器設定端點位址](#azure-monitor-configuration-endpoint-addresses)：443 | TCP  |
| Active Directory （如果適用） | ADX 子網 | AzureActiveDirectory：443 | TCP |
| 憑證授權單位 | ADX 子網 | 網際網路：80 | TCP |
| 內部通訊  | ADX 子網  | ADX 子網：所有埠  | 全部  |
| 用於 `sql\_request` 和外掛程式的埠 `http\_request`  | ADX 子網  | 網際網路：自訂  | TCP  |

### <a name="relevant-ip-addresses"></a>相關的 IP 位址

#### <a name="azure-data-explorer-management-ip-addresses"></a>Azure 資料總管管理 IP 位址

> [!NOTE]
> 針對未來的部署，請使用 AzureDataExplorer 服務標記

| 區域 | 位址 |
| --- | --- |
| 澳大利亞中部 | 20.37.26.134 |
| 澳大利亞 Central2 | 20.39.99.177 |
| 澳大利亞東部 | 40.82.217.84 |
| 澳大利亞東南部 | 20.40.161.39 |
| BrazilSouth | 191.233.25.183 |
| 加拿大中部 | 40.82.188.208 |
| 加拿大東部 | 40.80.255.12 |
| 印度中部 | 40.81.249.251, 104.211.98.159 |
| 美國中部 | 40.67.188.68 |
| 美國中部 EUAP | 40.89.56.69 |
| 中國東部 2 | 139.217.184.92 |
| 中國北部 2 | 139.217.60.6 |
| 東亞 | 20.189.74.103 |
| 美國東部 | 52.224.146.56 |
| 美國東部 2 | 52.232.230.201 |
| 東部美國 2 EUAP | 52.253.226.110 |
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
| US DoD 中部 | 52.182.33.66 |
| US DoD 東部 | 52.181.33.69 |
| 美國政府亞利桑那州 | 52.244.33.193 |
| 美國政府德克薩斯州 | 52.243.157.34 |
| 美國政府維吉尼亞州 | 52.227.228.88 |
| 美國中西部 | 52.159.55.120 |
| West Europe | 51.145.176.215 |
| 印度西部 | 40.81.88.112, 104.211.160.120 |
| 美國西部 | 13.64.38.225 |
| 美國西部 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>健全狀況監視位址

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
| 中國東部 2 | 40.73.96.39 |
| 中國北部 2 | 40.73.33.105 |
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
| US DoD 中部 | 52.238.116.34 |
| US DoD 東部 | 52.238.116.34 |
| 美國政府亞利桑那州 | 52.244.48.35 |
| 美國政府德克薩斯州 | 52.238.116.34 |
| 美國政府維吉尼亞州 | 23.97.0.26 |
| 美國中西部 | 168.61.212.201 |
| West Europe | 23.97.212.5 |
| 印度西部 | 23.99.5.162 |
| 美國西部 | 23.99.5.162 |
| 美國西部 2 | 23.99.5.162, 104.210.32.14 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Azure 監視器設定端點位址

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
| US DoD 中部 | 13.72.37.111 |
| US DoD 東部 | 13.72.37.111 |
| 美國政府亞利桑那州 | 13.72.37.111 |
| 美國政府德克薩斯州 | 13.72.37.111 |
| 美國政府維吉尼亞州 | 13.72.37.111 |
| 美國中西部 | 52.161.31.69 |
| West Europe | 52.174.4.112 |
| 印度西部 | 13.71.25.187 |
| 美國西部 | 40.78.70.148 |
| 美國西部 2 | 52.151.20.103 |

## <a name="disable-access-to-azure-data-explorer-from-the-public-ip"></a>停用從公用 IP 存取 Azure 資料總管

如果您想要完全停用透過公用 IP 位址存取 Azure 資料總管，請在 NSG 中建立另一個輸入規則。 此規則必須有較低的[優先順序](/azure/virtual-network/security-overview#security-rules)（較高的數位）。 

| **使用**   | **Source** | **來源服務標記** | **來源連接埠範圍**  | **Destination** | **目的地連接埠範圍** | * * 通訊協定 * * | **動作** | * * 優先順序 * * |
| ---   | --- | --- | ---  | --- | --- | --- | --- | --- |
| 停用來自網際網路的存取 | 服務標記 | 網際網路 | *  | VirtualNetwork | * | 任意 | Deny | 數位高於上述規則 |

此規則可讓您只透過下列 DNS 記錄（對應至每個服務的私人 IP）連接到 Azure 資料總管叢集：
* `private-[clustername].[geo-region].kusto.windows.net`搜尋引擎優化
* `private-ingest-[clustername].[geo-region].kusto.windows.net`（資料管理）

## <a name="expressroute-setup"></a>ExpressRoute 設定

使用 ExpressRoute 將內部部署網路連線到 Azure 虛擬網路。 常見的設定是透過邊界閘道協定（BGP）會話通告預設路由（0.0.0.0/0）。 這會強制將來自虛擬網路的流量轉送到客戶的內部網路，以捨棄流量，進而導致輸出流量中斷。 若要克服此預設值，可以設定[使用者定義的路由（UDR）](/azure/virtual-network/virtual-networks-udr-overview#user-defined) （0.0.0.0/0），而下一個躍點將是*網際網路*。 由於 UDR 的優先順序高於 BGP，因此流量會傳送到網際網路。

## <a name="securing-outbound-traffic-with-firewall"></a>使用防火牆保護輸出流量

如果您想要使用[Azure 防火牆](/azure/firewall/overview)或任何虛擬裝置來保護輸出流量，以限制功能變數名稱，防火牆中必須允許下列完整功能變數名稱（FQDN）。

```
prod.warmpath.msftcloudes.com:443
gcs.prod.monitoring.core.windows.net:443
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

您也需要在子網上定義[路由表](/azure/virtual-network/virtual-networks-udr-overview)，其中包含[管理位址](#azure-data-explorer-management-ip-addresses)和[健全狀況監視位址](#health-monitoring-addresses)與下一個躍點*網際網路*，以避免非對稱式路由問題。

例如，針對**美國西部**區域，必須定義下列 udr：

| 名稱 | 位址首碼 | 下一個躍點 |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | 網際網路 |
| ADX_Monitoring | 23.99.5.162/32 | 網際網路 |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本將 Azure 資料總管叢集部署至您的 VNet

若要將 Azure 資料總管叢集部署至您的虛擬網路，請使用將[azure 資料總管叢集部署至您的 VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure Resource Manager 範本。

此範本會建立叢集、虛擬網路、子網、網路安全性群組和公用 IP 位址。
