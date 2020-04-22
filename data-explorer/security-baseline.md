---
title: 資料資源管理員的 Azure 安全基線
description: 資料資源管理員的 Azure 安全基線
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e67621e92af12c45ac7e66bd6055f90245f705d2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501317"
---
# <a name="azure-security-baseline-for-data-explorer"></a>資料資源管理員的 Azure 安全基線

數據資源管理員的 Azure 安全基線包含可説明您改進部署安全狀態的建議。

此服務的基線取自[Azure 安全基準版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview),它提供了有關如何使用最佳實務指南在 Azure 上保護雲端解決方案的建議。

有關詳細資訊,請參閱[Azure 安全基線概述](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*有關詳細資訊,請參閱[安全控制:網路安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:使用虛擬網路上的網路安全組或 Azure 防火牆保護資源

**指南**:Azure 數據資源管理員支援將群集部署到虛擬網路中的子網。 此功能使您能夠在 Azure 資料資源管理器群集流量上強制實施網路安全組 (NSG) 規則,將本地網路連接到 Azure 資料資源管理器群集的子網,以及使用服務終結點保護數據連接源(事件中心和事件網格)。

如何將 Azure 資料資源管理器叢集部署到虛擬網路中:https://docs.microsoft.com/azure/data-explorer/vnet-deployment


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: 監視和記錄 Vnet、子網和 NICS 的配置和流量

**指南**:啟用網路安全組 (NSG) 串流日誌並將日誌發送到存儲帳戶進行流量審核。

如何啟用 NSG 串流紀錄:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

瞭解 Azure 安全中心提供的網路安全:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="13-protect-critical-web-applications"></a>1.3: 保護關鍵 Web 應用程式

**指導**:不適用;建議適用於在 Azure 應用服務或計算資源上運行的 Web 應用程式。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 拒絕與已知惡意 IP 位址的通訊

**指南**:在虛擬網路上啟用 Azure DDoS 保護標準,保護您的數據資源管理器群集,以抵禦 DDoS 攻擊。 使用 Azure 安全中心整合威脅智慧拒絕與已知惡意或未使用的 Internet IP 位址的通訊。

如何設定 DDoS 保護:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

瞭解 Azure 安全中心整合威脅情報:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 記錄網路封包與串流紀錄

**指南**:在用於保護 Azure 數據資源管理器群集的網路安全組 (NSG) 上啟用流日誌,並將日誌發送到存儲帳戶以進行流量審核。

如何啟用 NSG 串流紀錄:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: 部署基於網路的入侵偵測/入侵防禦系統

**指導**:不適用;此控件在終結點或防火牆上完成。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: 管理 Web 應用程式的流量

**指導**:不適用;此建議適用於在 Azure 應用服務或計算資源上運行的 Web 應用程式。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 最大限度降低網路安全規則的複雜性和管理開銷

**指南**:使用虛擬網路服務標記定義與 Azure 資料資源管理器群集關聯的網路安全組或 Azure 防火牆上的網路存取控制項。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 通過在規則的相應源或目標欄位中指定服務標記名稱(例如 ApiManagement),可以允許或拒絕相應服務的流量。 Microsoft 管理服務標記包含的位址首碼,並在位址更改時自動更新服務標記。

瞭解並使用服務標記:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Azure 資料資源管理員的服務標記設定要求:https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 維護網路設備的標準安全設定

**指導**:客戶使用 Azure 策略定義和實施網路資源的標準安全配置。

客戶還可以使用 Azure 藍圖通過在單個藍圖定義中打包關鍵環境工件(如 ARM 範本、RBAC 控件和策略)來簡化大規模 Azure 部署。 輕鬆將藍圖應用於新訂閱和環境,並通過版本控制微調控制和管理。

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何建立 Azure 藍圖:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10: 文件流量設定規則

**指南**:對網路安全組 (NSG) 和其他與數據資源管理器群集的網路安全和流量流相關的資源使用標記。 對於單個 NSG 規則,使用「描述」欄位指定允許流量從網路傳輸的任何規則的業務需求和/或持續時間(等)。

如何建立與使用標籤:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 使用自動工具監控網路資源設定並偵測變更

**指導**:使用 Azure 策略驗證(和/或修復)網路資源的配置。

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*有關詳細資訊,請參閱[安全控制:日誌記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 使用批准的時間同步來源

**指南**:Microsoft 維護 Azure 資源的時間源,客戶可以更新客戶擁有的計算部署的時間同步。

如何為 Azure 計算資源設定時間同步:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure 安全中心監視**:不適用

**責任**: 微軟

### <a name="22-configure-central-security-log-management"></a>2.2: 配置中央安全日誌管理

**指南**: Azure 資料資源管理器使用診斷日誌來深入瞭解引入成功和失敗。 您可以將操作日誌匯出到 Azure 存儲、事件中心或日誌分析以監視引入狀態。

如何監控 Azure 資料資源管理員引入操作:

https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

如何在 Azure 資料資源管理器中引入和查詢監視資料:

https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:為 Azure 資源啟用稽核紀錄記錄

**指南**:為 Azure 數據資源管理器啟用診斷設置,以便訪問和日誌記錄以服務特定操作和日誌記錄。 Azure 監視器中的 Azure 活動日誌(包括有關資源的高級紀錄記錄)默認情況下處於啟用狀態。

如何監控 Azure 資料資源管理員引入操作:https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

如何使用 Azure 監視器收集平台日誌和指標:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Azure 平台日誌概述:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: 從作業系統收集安全日誌

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5: 設定安全紀錄儲存保留

**指南**:在 Azure 監視器中,根據組織的合規性法規設置日誌分析工作區保留期。 使用 Azure 儲存帳戶進行長期/存檔存儲。

如何為紀錄分析工作區設定紀錄保留參數:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="26-monitor-and-review-logs"></a>2.6: 監視和審閱日誌

**指導**:分析並監控日誌是否存在異常行為,並定期查看結果。 為 Azure 資料資源管理器啟用診斷設定後,使用 Azure 監視器的日誌分析工作區查看日誌並執行日誌數據的查詢。

瞭解紀錄分析工作區:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure 監視器中執行自訂查詢:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 啟用例外活動警示

**指導**:不適用;Azure 資料資源管理器沒有此功能。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="28-centralize-anti-malware-logging"></a>2.8: 集中反惡意軟體紀錄記錄

**指導**:不適用;Azure 資料資源管理員不處理反惡意軟體記錄。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="29-enable-dns-query-logging"></a>2.9: 啟用 DNS 查詢紀錄記錄

**指南**:不適用:DNS 查詢不是 Azure 數據資源管理員的函數。


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10: 啟用命令列稽核紀錄記錄

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**:不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*有關詳細資訊,請參閱[安全控制:識別和存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: 維持管理帳號清單

**指南**:在 Azure 數據資源管理器中,安全角色定義哪些安全主體(使用者和應用程式)有權對安全資源(如資料庫或表)進行操作,以及允許執行哪些操作。  可以利用 Kusto 查詢來列出 Azure 資料資源管理器群集和資料庫的管理角色中的原則。
[使用 Kusto 查詢的 Azure 資料資源管理員中的安全角色管理](kusto/management/security-roles.md)



**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 在適用的情況下變更預設密碼

**指南**:Azure AD 沒有預設密碼的概念。 需要密碼的其他 Azure 資源強制創建具有複雜性要求和最小密碼長度的密碼,具體取決於服務。 您負責可能使用預設密碼的第三方應用程式和市場服務。


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: 確保使用專用管理帳戶

**指導**:客戶圍繞使用專用管理帳戶制定規範的操作程式。 使用 Azure 安全中心識別和訪問管理監視管理帳戶數。

客戶還可以使用適用於 Microsoft 服務的 Azure AD 特權識別管理特權角色和 Azure ARM 啟用"及時/剛剛足夠"訪問。 

什麼是 Azure AD 特權標識管理?https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4:使用 Azure 活動目錄的單一登入 (SSO)

**指南**:在可能的情況下,客戶將 SSO 與 Azure 活動目錄 (Azure AD) 一起使用,而不是配置每個服務的個人獨立認證。 使用 Azure 安全中心標識和存取管理建議。

使用 Azure AD 瞭解 SSO:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5:對所有基於 Azure 活動目錄的訪問使用多重身份驗證。

**指南**:啟用 Azure 活動目錄 (Azure AD) 多重身份驗證 (MFA), 並遵循 Azure 安全中心標識和存取管理建議。

如何在 Azure 中啟用 MFA:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted 

如何在 Azure 安全中心內監視標識和訪問:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="36-use-of-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 對所有管理工作使用專用電腦 (特權存取工作站)

**指南**:使用設定為登入和配置 Azure 資源的多重身份驗證 (MFA) 的 PAW(特權存取工作站)。

瞭解特權存取工作站:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中啟用 MFA:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="37-log-and-alert-on-suspicious-activity-on-administrative-accounts"></a>3.7: 記錄和警報管理帳戶上的可疑活動

**指南**:當環境中發生可疑或不安全活動時,請使用 Azure 活動目錄 (Azure AD) 安全報告生成日誌和警報。 使用 Azure 安全中心監視識別和存取活動

如何辨識標記為有風險活動的 Azure AD 使用者:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

如何監視 Azure 安全中心中的使用者識別和訪問活動:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8:僅從已批准的位置管理 Azure 資源

**指南**:客戶使用條件訪問命名位置,僅允許從 IP 位址範圍或國家/地區的特定邏輯分組進行訪問。

如何在 Azure 中設定命名位置:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="39-utilize-azure-active-directory"></a>3.9: 利用 Azure 的目錄

**指南**: Azure 活動目錄 (Azure AD) 是驗證 Azure 資料資源管理員的首選方法。 其支援數種驗證案例：

使用者驗證 (互動式登入)：用來驗證人為主體。

應用程式驗證 (非互動式登入) ：用來驗證必須執行/驗證而不存在任何人為使用者的服務和應用程式。

[Azure 資料資源管理員存取控制概述](kusto/management/access-control/index.md)

[使用 Azure 活動目錄進行認證](kusto/management/access-control/aad.md)



**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 定期檢視和協調使用者存取

**指南**: Azure 活動目錄 (Azure AD) 提供日誌以幫助發現陳舊的帳戶。 此外,使用 Azure 標識存取審核可高效地管理組成員身份、對企業應用程式的訪問和角色分配。 可以定期查看使用者的訪問許可權,以確保只有正確的使用者才能繼續訪問。 

[使用 Azure AD 進行 Azure 資料資源管理員存取的如何進行身份驗證](kusto/management/access-control/how-to-authenticate-with-aad.md)

[Azure AD 報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

[如何使用 Azure 識別存取稽核](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 監視訪問已停用帳戶的嘗試

**指南**:您可以使用 Azure 活動目錄 (Azure AD) 登入活動、審核和風險事件日誌源進行監視,以便與任何安全資訊和事件管理 (SIEM) / 監視工具集成。

 您可以通過為 Azure 活動目錄使用者帳戶創建診斷設定、將審核日誌和登錄日誌發送到日誌分析工作區來簡化此過程。 客戶在日誌分析工作區中配置所需的警報。

如何將 Azure 活動紀錄整合到 Azure 監視器中:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 帳戶登錄行為偏差警報

**指南**:使用 Azure 活動目錄 (Azure AD) 風險檢測和身份保護功能配置自動回應,以識別與使用者身份相關的可疑操作。 此外,您可以將數據引入 Azure Sentinel 以進行進一步調查。

如何檢視 Azure AD 風險登入:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何設定與啟用身份保護風險原則:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 在支援方案期間向 Microsoft 提供對相關客戶資料的存取

**指南**:在 Microsoft 需要存取客戶資料的支援方案中,客戶密碼箱提供了一個介面,供客戶審閱、批准或拒絕客戶數據訪問請求。

瞭解客戶密碼箱:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

## <a name="data-protection"></a>資料保護

*有關詳細資訊,請參閱[安全控制:資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 維護敏感資訊清單

**指南**:使用標記來協助追蹤記憶體或處理敏感資訊的 Azure 資源。

如何建立與使用標籤:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 隔離記憶體或處理敏感資訊的系統

**指導**:為開發、測試和生產實施單獨的訂閱和/或管理組。 Azure 資料資源管理器群集應透過虛擬網路/子網與其他資源分開,在網路安全組 (NSG) 或 Azure 防火牆中正確標記和保護。 存儲或處理敏感數據的數據資源管理器群集應足夠隔離。

如何建立其他 Azure 訂閱:https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組:https://docs.microsoft.com/azure/governance/management-groups/create

如何建立與使用標籤:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何將 Azure 資料資源管理器叢集部署到虛擬網路中:https://docs.microsoft.com/azure/data-explorer/vnet-deployment

如何建立具有安全設定的 NSG:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 監控和阻止未經授權傳輸敏感資訊

**指導**:不適用;對於由 Microsoft 管理的基礎平臺,Microsoft 將所有客戶內容視為敏感內容,並竭盡全力防止客戶數據丟失和暴露。 為了確保 Azure 中的客戶數據保持安全,Microsoft 已經實施並維護了一套強大的數據保護控制和功能。

瞭解 Azure 的客戶資料保護:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:加密傳輸中的所有敏感資訊

**指南**: 預設情況下,Azure 資料資源管理器群集協商 TLS 1.2。 確保連接到 Azure 資源的任何用戶端能夠協商 TLS 1.2 或更高。

**Azure 安全中心監視**:不適用

**責任**: 共用

### <a name="45-divuse-an-active-discovery-tool-to-identify-sensitive-datadiv"></a>4.5: <div>使用活動探索工具識別敏感資料</div>

**指南**:Azure 數據資源管理員尚不可用數據識別、分類和損失預防功能。 如果需要符合性,則實施第三方解決方案。

對於由 Microsoft 管理的基礎平臺,Microsoft 將所有客戶內容視為敏感內容,並竭盡全力防止客戶數據丟失和暴露。 為了確保 Azure 中的客戶數據保持安全,Microsoft 已經實施並維護了一套強大的數據保護控制和功能。

瞭解 Azure 的客戶資料保護:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="46-divuse-azure-rbac-to-control-access-to-resourcesdiv"></a>4.6: <div>使用 Azure RBAC 控制對資源的存取</div>

**指南**:Azure 數據資源管理員使您能夠使用基於角色的訪問控制 (RBAC) 模型控制對資料庫和表的訪問。 在此模型中，主體 (使用者、群組和應用程式) 會對應至角色。 主體可以根據其指派的角色存取資源。

關於如何為 Azure 資料資源管理員設定 RBAC 的角色、權限以及說明的清單:https://docs.microsoft.com/azure/data-explorer/manage-database-permissions


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 使用基於主機的數據丟失防護來實施訪問控制

**指導**:不適用;此建議用於計算資源。

Microsoft 管理 Azure 資料資源管理員的基礎基礎結構,並實施了嚴格的控制,以防止客戶數據丟失或暴露。

瞭解 Azure 的客戶資料保護:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 靜態加密敏感資訊

**指南**:Azure 磁碟加密有助於保護數據,以滿足組織安全性和合規性承諾。 它為群集虛擬機器的作業系統和數據磁碟提供卷加密。 它還與 Azure 密鑰保管庫集成,這使我們能夠控制和管理磁碟加密密鑰和機密,並確保在 Azure 儲存中靜態時對 VM 磁碟上的所有數據進行加密。

如何為 Azure 資料資源管理器叢集啟用靜態加密:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 對關鍵 Azure 資源的更改進行紀錄和警報

**指南**:將 Azure 監視器與 Azure 活動紀錄一起用於創建 Azure 資料資源管理器群集上資源級更改時的警報。

如何為 Azure 活動紀錄事件建立警報:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

如何為 Azure 活動紀錄事件建立警報:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure 安全中心監視**:是

**責任**: 客戶

## <a name="vulnerability-management"></a>弱點管理

*有關詳細資訊,請參閱[安全控制:漏洞管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 執行自動漏洞掃描工具

**指南**:按照 Azure 安全中心的建議,保護 Azure 數據資源管理器資源。

Microsoft 還會在支援 Azure 資料資源管理員的基礎系統上執行漏洞管理。

瞭解 Azure 安全中心建議:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Azure 安全中心監視**:是

**責任**: 微軟

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:部署自動化作業系統修補程式管理解決方案

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:部署自動第三方軟體修補程式管理解決方案

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 比較背對背漏洞掃描

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="55-utilize-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 利用風險評級流程確定修復已發現漏洞的優先順序。

**指南**:使用 Azure 安全中心提供的預設風險評級(安全分數)。
瞭解 Azure 安全中心安全分數:https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure 安全中心監視**:是

**責任**: 客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*有關詳細資訊,請參閱[安全控制:庫存和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-utilize-azure-asset-discovery"></a>6.1: 利用 Azure 資產發現

**指南**:使用 Azure 資源圖查詢/發現訂閱中的所有資源(如計算、存儲、網路、埠和協定等)。 請確保租戶中的適當(讀取)許可權,並枚舉訂閱中的所有 Azure 訂閱以及資源。

儘管可以通過資源圖發現經典 Azure 資源,但強烈建議今後創建和使用 Azure 資源管理器資源。

如何使用 Azure 資源圖建立查詢:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何檢視 Azure 訂閱:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

瞭解 Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="62-maintain-asset-metadata"></a>6.2: 維護資產元資料

**指南**:將標記應用於 Azure 資源,使元數據以邏輯方式將它們組織到分類中。

如何建立與使用標籤:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 刪除未經授權的 Azure 資源

**指南**:您可以酌情使用適當的命名約定、標記、管理組或單獨的訂閱來組織和跟蹤資產。 您可以使用 Azure 資源圖定期協調庫存,並確保及時從訂閱中刪除未經授權的資源。 

如何建立其他 Azure 訂閱:https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組:https://docs.microsoft.com/azure/governance/management-groups/create

如何建立與使用標籤:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何使用 Azure 資源圖建立查詢:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:維護已批准的 Azure 資源和軟體標題的清單。

**指南**:您需要根據組織需要創建已批准的 Azure 資源和已批准的軟體的清單,以便計算資源。  


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 監視未經批准的 Azure 資源

**指南**:您可以使用 Azure 策略對客戶訂閱中可以使用以下內建策略定義建立的資源類型施加限制:

    - 不允許的資源類型

    - 允許的資源類型

您會使用 

可以使用 Azure 監視器監視的活動日誌。

此外,您可以使用 Azure 資源圖查詢/發現訂閱中的資源。

教學:建立與管理原則以強制實施合規性:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

快速入門:使用 Azure 資源圖資源管理員執行第一個資源圖查詢:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

使用 Azure 監視器建立、檢視與管理活動紀錄警報:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 監控計算資源中的未經批准的軟體應用程式

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 刪除未經批准的 Azure 資源和軟體應用程式

**指導**:不適用;此建議適用於計算資源和整個 Azure。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="68-utilize-only-approved-applications"></a>6.8: 僅利用已批准的申請

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="69-utilize-only-approved-azure-services"></a>6.9: 僅利用已批准的 Azure 服務

**指南**:您可以使用 Azure 策略對客戶訂閱中可以使用以下內建策略定義建立的資源類型施加限制:

    - 不允許的資源類型

    - 允許的資源類型

教學:建立與管理原則以強制實施合規性:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 政策範例:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="610-implement-approved-application-list"></a>6.10: 實施已批准的申請清單

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11:限制使用者透過文稿與 Azure 資源管理員互動的能力

**指南**:使用 Azure 條件訪問通過為"Microsoft Azure 管理"應用配置"阻止存取"來限制使用者與 Azure 資源管理員互動的能力。 這將阻止 Azure 訂閱中的資源的創建和更改。 

使用條件存取管理對 Azure 管理的存取:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 限制使用者在計算資源中執行文稿的能力

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 物理或邏輯隔離高風險應用程式

**指導**:不適用;此建議適用於在 Azure 應用服務或計算資源上運行的 Web 應用程式。

**Azure 安全中心監視**:不適用

**責任**:不適用

## <a name="secure-configuration"></a>安全設定

*有關詳細資訊,請參閱[安全控制:安全設定](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:為所有 Azure 資源建立安全配置

**指南**:使用 Azure 策略別名創建自訂策略以審核或強制執行 Azure 資源的配置。 您還可以使用內置的 Azure 策略定義。

此外,Azure 資源管理員能夠匯出 JavaScript 物件符號 (JSON) 中的範本,應對其進行審查,以確保配置滿足/超過組織的安全要求。

您還可以使用 Azure 安全中心的建議作為 Azure 資源的安全配置基線。

如何檢視可用的 Azure 政策別名:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

教學:建立與管理原則以強制實施合規性:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

單個資源和多資源匯出到 Azure 門戶中的樣本:https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal

安全建議 - 參考指南:https://docs.microsoft.com/azure/security-center/recommendations-reference



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: 為您的作業系統建立安全性設定

**指導**:不適用;本指南用於計算資源。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3:維護所有 Azure 資源的安全設定

**指導**:使用 Azure 策略 [拒絕] 和 [部署(如果不存在))在整個 Azure 資源中強制實施安全設置。  您可以使用更改追蹤、策略合規性儀表板或自定義解決方案等解決方案輕鬆辨識環境中的安全更改。

瞭解 Azure 政策效果:https://docs.microsoft.com/azure/governance/policy/concepts/effects

建立與管理原則以強制實施合規性:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

使用「更改追蹤」解決方案追蹤環境中的變更:https://docs.microsoft.com/azure/automation/change-tracking

取得 Azure 資源的合規性資料:https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: 維護作業系統的安全設定

**指導**:不適用;本指南用於計算資源。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: 安全儲存 Azure 資源配置

**指南**:使用 Azure 儲存庫安全地儲存和管理代碼,如自定義 Azure 策略、Azure 資源管理員範本、所需狀態配置腳本等。要造訪在 Azure DevOps 中管理的資源,可以授予或拒絕特定使用者、內置安全組或與 Azure DevOps 整合或在 Azure 活動目錄(如果與 TFS 整合)中定義的組的許可權。

如何在 Azure DevOps 中儲存代碼:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

有關 Azure DevOps 中的權限與群組:https://docs.microsoft.com/azure/devops/organizations/security/about-permissions



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 安全地儲存自訂作業系統映像

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 部署系統設定管理工具

**指南**:使用 Azure 策略定義和實現 Azure 資源的標準安全配置。 使用 Azure 策略別名創建自訂策略以審核或強制執行 Azure 資源的網路配置。 您還可以使用與您的特定資源相關的內置策略定義。  此外,您可以使用 Azure 自動化來部署配置更改。

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用別名:https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:部署作業系統的系統設定管理工具

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: 實現 Azure 服務的自動設定監視

**指南**:使用 Azure 策略別名創建自定義策略以警報、審核和強制執行系統配置。 使用 Azure 策略 [審核]、[拒絕]和 [部署(如果不存在))自動強制 Azure 資源配置。

如何設定與管理 Azure 政策:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 實現作業系統的自動設定監控

**指導**:不適用;此建議用於計算資源。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="711-securely-manage-azure-secrets"></a>7.11: 安全管理 Azure 機密

**指南**:Azure 磁碟加密為 Azure 數據資源管理器群集虛擬機器的作業系統和資料磁碟提供卷加密。 它還與 Azure 密鑰保管庫整合,它允許您控制和管理磁碟加密金鑰和機密,並確保在 Azure 儲存中靜態時對 VM 磁碟上的所有數據進行加密。

如何在 Azure 資料資源管理員保護群集:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Azure 安全中心監視**:目前不可用

**責任**: 微軟

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: 安全自動管理身份

**指南**:使用託管標識在 Azure AD 中為 Azure 服務提供自動託管標識。 託管標識允許您對支援 Azure AD 身份驗證的任何服務(包括密鑰保管庫)進行身份驗證,而無需在代碼中進行任何認證。如何設定託管識別:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

為 Azure 資料資源管理器叢集設定託管識別:https://docs.microsoft.com/azure/data-explorer/managed-identities


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 消除意外的認證

**指南**:實現憑據掃描程式以標識代碼中的憑據。 憑據掃描程式還將鼓勵將發現的憑據移動到更安全的位置,如 Azure 密鑰保管庫。 

如何設定認證程式:https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 安全中心監視**:不適用

**責任**: 客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*有關詳細資訊,請參閱[安全控制:惡意軟體防禦](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-utilize-centrally-managed-anti-malware-software"></a>8.1: 利用集中管理的反惡意軟體

**指導**:不適用;此建議用於計算資源。

在支援 Azure 服務的基礎主機(例如 Azure 資料資源管理員)上啟用 Microsoft 反惡意軟體,但不在客戶內容上運行。

**Azure 安全中心監視**:不適用

**責任**:不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:要上傳到非計算 Azure 資源的預入檔案

**指南**:在支援 Azure 服務的基礎主機上啟用 Microsoft 反惡意軟體(例如,Azure 數據資源管理員),但它不在客戶內容上運行。

預掃描上載到非計算 Azure 資源的任何內容,如 Azure 資料資源管理器、資料湖儲存、Blob 存儲、用於 PostgreSQL 的 Azure 資料庫等。在這種情況下,Microsoft 無法訪問您的數據。



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 確保更新反惡意軟體和簽名

**指導**:不適用;此建議用於計算資源。

Microsoft 反惡意軟體在支援 Azure 服務的基礎主機上啟用,但不在客戶內容上運行。

**Azure 安全中心監視**:不適用

**責任**:不適用

## <a name="data-recovery"></a>資料復原

*有關詳細資訊,請參閱[安全控制:資料恢復](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 確保定期自動備份

**指南**:資料資源管理器群集使用的 Microsoft Azure 儲存帳戶中的數據始終被複製,以確保持久性和高可用性。 「Azure 儲存體」會複製您的資料，以保護該資料不受計劃性和非計劃性事件影響，包括暫時性硬體故障、網路或電力中斷和大規模天然災害。 您可以選擇在相同資料中心內、在相同地區的不同資料中心內、或跨不同區域複寫您的資料。

瞭解 Azure 儲存冗餘和服務等級協定:https://docs.microsoft.com/azure/storage/common/storage-redundancy

[匯出資料匯出到儲存](kusto/management/data-export/export-data-to-storage.md)



**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 執行完整的系統備份並備份任何客戶託管金鑰

**指南**:Azure 數據資源管理器在靜態存儲帳戶中加密所有數據。 默認情況下,數據使用Microsoft管理的密鑰進行加密。 為了對加密金鑰進行其他控制,可以提供客戶管理的密鑰,用於數據加密。 客戶管理的金鑰必須存儲在 Azure 密鑰保管庫中。 

使用 C# 設定客戶託管金鑰 :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

使用 Azure 資源管理員樣本設定客戶管理金鑰:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager

如何備份 Azure 金鑰保管庫證書:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0


**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:驗證所有備份,包括客戶託管金鑰

**指南**:定期測試 Azure 密鑰保管庫機密的數據還原。

如何還原 Azure 金鑰保管庫證書:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0 

使用 C# 設定客戶託管金鑰 :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

使用 Azure 資源管理員樣本設定客戶管理金鑰:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4:確保保護備份和客戶管理金鑰

**指南**: 客戶在金鑰保管庫中啟用軟刪除,以保護密鑰免受意外或惡意刪除。  您還可以啟用清除保護,以便如果保管庫或對象處於已刪除狀態,則在保留期結束之前無法清除它。  

如何在金鑰保存庫中開啟軟刪除與清除保護:https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

使用 C# 設定客戶託管金鑰 :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

使用 Azure 資源管理員樣本設定客戶管理金鑰:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Azure 安全中心監視**:是

**責任**: 客戶

## <a name="incident-response"></a>事件回應

*有關詳細資訊,請參閱[安全控制:事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-incident-response-guide"></a>10.1:建立事件回應指南

**指導**:為您的組織構建事件回應指南。 確保有書面事件回應計劃,定義人員的所有角色以及事件處理/管理階段,從檢測到事件後審查。
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final

    



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2:建立事件評分和優先順序處理過程

**指南**:安全中心為每個警報分配嚴重性,以説明您優先處理應首先調查的警報。 嚴重性基於安全中心在查找中的信心程度或用於發出警報的分析,以及導致警報的活動背後存在惡意的置信度。 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

    Use tags to organize your Azure resources: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="103-test-security-response-procedures"></a>10.3: 測試安全回應程式

**指南**:執行練習,以常規節奏測試系統的事件回應功能,以幫助保護 Azure 資源。 找出薄弱環節和差距,並根據需要修訂計劃。
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Azure 安全中心監視**:不適用

**責任**: 客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: 提供安全事件連絡人詳細資訊,並&nbsp;配置安全 事件的警報通知

**指南**:如果 Microsoft 安全回應中心 (MSRC) 發現您的數據已被非法或未經授權的方訪問,Microsoft 將使用安全事件聯繫資訊與您聯繫。 事後查看事件,以確保問題得到解決。
    

如何設置 Azure 安全中心安全連絡人:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 安全中心監視**:是

**責任**: 客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 將安全警報納入事件回應系統

**指南**:使用"連續匯出"功能匯出 Azure 安全中心警報和建議,以幫助識別 Azure 資源的風險。 "連續匯出"允許您手動或持續、持續的方式匯出警報和建議。 您可以使用 Azure 安全中心數據連接器將警報流式傳輸到 Azure 哨兵。
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Azure 安全中心監視**:目前不可用

**責任**: 客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 自動回應安全警報

**指南**:使用 Azure 安全中心的工作流自動化功能通過安全警報和建議上的「邏輯應用」自動觸發回應,以保護 Azure 資源。
    

    How to configure Workflow Automation and Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation



**Azure 安全中心監視**:目前不可用

**責任**: 客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*有關詳細資訊,請參閱[安全控制:滲透測試和紅隊練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1:對 Azure 資源進行定期滲透測試,並確保在 60 天內修復所有關鍵安全發現

**指南**:請遵循 Microsoft 的接用規則,以確保您的滲透測試不違反https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1Microsoft 策略 : .

您可以在此處找到有關 Microsoft 針對 Microsoft 託管雲基礎架構、服務和應用程式進行紅色團隊和即時網站滲透測試的策略和實施的詳細資訊:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 安全中心監視**:不適用

**責任**: 共用

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 安全基準](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 瞭解有關[Azure 安全基線的更多資訊](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
