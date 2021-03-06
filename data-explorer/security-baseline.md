---
title: 適用于 Azure 資料總管的 azure 安全性基準
description: Azure 資料總管安全性基準提供程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d641714607bd1843e46ac708a9302c8a1dad6782
ms.sourcegitcommit: cffc81de2b5c75a0ef5a3c71ff58d1ef52d4eb5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95872323"
---
# <a name="azure-security-baseline-for-azure-data-explorer"></a>適用于 Azure 資料總管的 azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview-v1) 的指引套用至 azure 資料總管。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。
內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 azure 資料總管的相關指引來分組。 尚未排除適用于 Azure 資料總管的 **控制項**。

 
若要瞭解 Azure 資料總管如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 azure 資料總管安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導** 方針： Azure 資料總管支援將叢集部署到虛擬網路中的子網。 這項功能可讓您在 Azure 資料總管叢集流量上強制執行網路安全性群組 (NSG) 規則、將內部部署網路連線到 Azure 資料總管叢集的子網，並使用服務端點 (事件中樞和事件方格) 來保護您的資料連線來源。

- [如何在您的虛擬網路中建立叢集](https://docs.microsoft.com/azure/data-explorer/vnet-create-cluster-portal)

- [如何將 Azure 資料總管叢集部署到虛擬網路](https://docs.microsoft.com/azure/data-explorer/vnet-deployment)

- [如何針對您的虛擬網路叢集建立、連線能力和操作進行疑難排解](https://docs.microsoft.com/azure/data-explorer/vnet-deploy-troubleshoot?tabs=windows)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：監視和記錄虛擬網路、子網和網路介面的設定和流量

**指導** 方針：啟用網路安全性群組 (NSG) 流量記錄，並將記錄傳送至儲存體帳戶以進行流量審核。

- [如何啟用 NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [瞭解 Azure 資訊安全中心所提供的網路安全性](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導** 方針：在保護 Azure 資料總管叢集的虛擬網路上啟用 Azure DDoS 保護標準，以防範 DDoS 攻擊。 使用 Azure 資訊安全中心的整合式威脅情報，以拒絕與已知為惡意或未使用的網際網路 IP 位址通訊。

- [如何設定 DDoS 保護](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [了解 Azure 資訊安全中心的整合式威脅情報](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導** 方針：在網路安全性群組上啟用流量記錄 (NSG) 用來保護您的 Azure 資料總管叢集，並將記錄傳送至儲存體帳戶以進行流量審核。

- [如何啟用 NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導** 方針：使用虛擬網路服務標籤，來定義與您的 azure 資料總管叢集相關聯的網路安全性群組或 azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 在規則的適當來源或目的地欄位中指定服務標籤名稱 (例如 ApiManagement)，即可允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

- [了解和使用服務標籤](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Azure 資料總管的服務標記設定需求](https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針：客戶可以使用 Azure 原則來定義和實行網路資源的標準安全性設定。

客戶也可以使用 Azure 藍圖，藉由在單一藍圖定義中封裝關鍵環境成品（例如 Azure Resource Manager 範本、Azure RBAC 控制項和 Azure 原則指派）來簡化大規模的 Azure 部署。 輕鬆地將藍圖套用至新的訂閱、環境，以及透過版本控制來微調控制和管理。

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何建立 Azure 藍圖](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導** 方針：針對網路安全性群組使用標籤 (NSG) 以及與 Azure 資料總管叢集的網路安全性和流量流程相關的其他資源。 對於個別的 NSG 規則，使用 [描述] 欄位，針對允許進出網路流量的任何規則指定商務需求和/或持續時間 (等等)。

- [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 Azure 原則來驗證網路資源的 (及/或補救) 設定。

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針： Azure 資料總管會使用診斷記錄來取得內嵌成功與失敗的見解。 您可以將作業記錄匯出至 Azure 儲存體、事件中樞或 Log Analytics，以監視內嵌狀態。

- [如何監視 Azure 資料總管內嵌作業](https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs)

- [如何在 Azure 資料總管中內嵌和查詢監視資料](https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：啟用適用于 Azure 資料總管的診斷設定，以進行存取和記錄，以服務特定的作業和記錄。 預設會啟用 Azure 監視器中的 Azure 活動記錄，其中包含有關資源的高階記錄。

- [如何監視 Azure 資料總管內嵌作業](https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs)

- [如何使用 Azure 監視器收集平臺記錄和計量](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Azure 平台記錄概觀](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指引**：在 Azure 監視器中，根據貴組織的合規性規定來設定您的 Log Analytics 工作區保留期間。 使用 Azure 儲存體帳戶進行長期/封存儲存。

- [如何設定 Log Analytics 工作區的記錄保留期參數](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導** 方針：分析和監視異常行為的記錄，並定期查看結果。 啟用 Azure 資料總管的診斷設定之後，請使用 Azure 監視器的 Log Analytics 工作區來檢查記錄，並對記錄資料執行查詢。

- [瞭解 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

- [如何在 Azure 監視器中執行自訂查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導** 方針：在 Azure 資料總管中，安全性角色會定義 (使用者和應用程式的安全性主體) 有權在安全的資源（例如資料庫或資料表）上操作，以及允許的作業。 您可以利用 Kusto 查詢來列出 Azure 資料總管叢集和資料庫的系統管理員角色中的原則。

- [使用 Kusto 查詢在 Azure 資料總管中管理安全性角色](https://docs.microsoft.com/azure/kusto/management/security-roles)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導** 方針： Azure Active Directory (Azure AD) 沒有預設密碼的概念。 需要密碼的其他 Azure 資源會強制建立具有複雜性需求和密碼長度下限的密碼，這會因服務而有所不同。 您必須負責可能使用預設密碼的協力廠商應用程式和 marketplace 服務。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導** 方針：客戶可以使用專用的系統管理帳戶來建立標準作業程式。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

客戶也可以使用 Azure Active Directory (Azure AD) Privileged Identity Management Microsoft 服務和 Azure ARM 的特殊許可權角色，來啟用及時且足夠的存取權。 

- [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 單一登入 (SSO) 

**指導** 方針：任何可能的情況下，客戶可以使用 SSO 搭配 Azure Active Directory (Azure AD) ，而不是為每個服務設定個別的獨立認證。 使用 Azure 資訊安全中心身分識別和存取管理建議。

- [瞭解 Azure AD 的 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：所有以 Azure Active Directory 為基礎的存取都使用多重要素驗證

**指導** 方針：啟用 Azure Active Directory (AZURE AD) MFA (的多重要素驗證，並遵循) 身分識別和存取管理建議。

- [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [如何在 Azure 資訊安全中心監視身分識別和存取](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用安全、受 Azure 管理的工作站進行系統管理工作

**指導** 方針：使用 paw (特殊許可權的存取工作站) 搭配多重要素驗證 (MFA) 設定為登入和設定 Azure 資源。

- [瞭解特殊權限存取工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導** 方針：當環境中發生可疑或不安全的活動時，使用 Azure Active Directory (Azure AD) 的安全性報告來產生記錄和警示。 使用 Azure 資訊安全中心來監視身分識別和存取活動

- [如何識別已標示為有風險活動的 Azure AD 使用者](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [如何在 Azure 資訊安全中心中監視使用者身分識別和存取活動](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導** 方針：客戶可以使用名為「位置」的條件式存取，只允許來自特定 IP 位址範圍或國家/地區的邏輯群組進行存取。

- [如何在 Azure 中設定命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針： Azure Active Directory (Azure AD) 是向 Azure 資料總管驗證的慣用方法。 Azure AD 支援多種驗證案例：

- 使用者驗證 (互動式登入)：用來驗證人為主體。

- 應用程式驗證 (非互動式登入) ：用來驗證必須執行/驗證而不存在任何人為使用者的服務和應用程式。

如需詳細資訊，請參閱下列參考資料：

- [Azure 資料總管存取控制總覽](https://docs.microsoft.com/azure/kusto/management/access-control)

- [使用 Azure Active Directory 進行驗證](https://docs.microsoft.com/azure/kusto/management/access-control/aad)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導** 方針： Azure Active Directory (Azure AD) 提供記錄以協助探索過時的帳戶。 此外，使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期審核使用者的存取權，以確保只有適當的使用者可以繼續存取。 

- [如何向 Azure 資料總管存取 Azure AD 進行驗證](https://docs.microsoft.com/azure/kusto/management/access-control/how-to-authenticate-with-aad)

- [Azure AD 報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 身分識別存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導** 方針：您可以使用 Azure Active Directory (Azure AD) 登入活動、Audit 和風險事件記錄檔來源進行監視，讓您可以整合任何安全性資訊和事件管理 (SIEM) /監視工具。

 您可以建立 Azure Active Directory 使用者帳戶的診斷設定、將審核記錄和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 客戶可在 Log Analytics 工作區中設定所需的警示。

- [如何將 Azure 活動記錄整合到 Azure 監視器中](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導** 方針：使用 Azure Active Directory (Azure AD) 風險偵測和 Identity Protection 功能，以針對偵測到與使用者身分識別相關的可疑動作，設定自動回應。 此外，您可以將資料內嵌到 Azure Sentinel 以進行進一步的調查。

- [如何檢視有風險的 Azure AD 登入](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何設定和啟用身分識別保護風險原則](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導** 方針：在 Microsoft 需要存取客戶資料的支援案例中，客戶加密箱提供了一個介面，讓客戶可以檢查及核准或拒絕客戶資料存取要求。

- [瞭解客戶加密箱](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。

- [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 Azure 資料總管叢集應由虛擬網路/子網與其他資源分開，並在網路安全性群組內適當地標記， (NSG) 或 Azure 防火牆。 儲存或處理敏感性資料的 Azure 資料總管叢集應充分隔離。

- [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [如何將 Azure 資料總管叢集部署到虛擬網路](https://docs.microsoft.com/azure/data-explorer/vnet-deployment)

- [如何建立具有安全性設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針： Azure 資料總管叢集預設會協商 TLS 1.2。 確定任何連線至 Azure 資源的用戶端都能夠協商 TLS 1.2 或更新版本。

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針： Azure 資料總管尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用角色型存取控制來控制資源的存取權

**指導** 方針： azure 資料總管可讓您使用 azure 角色型存取控制 (RBAC) 模型，來控制對資料庫和資料表的存取。 在此模型中，主體 (使用者、群組和應用程式) 會對應至角色。 主體可以根據其指派的角色存取資源。

- [角色和許可權的清單，以及如何針對 Azure 資料總管設定 RBAC 的指示](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針： Azure 磁碟加密協助保護您的資料，以符合組織的安全性和合規性承諾。 它會針對叢集虛擬機器的 OS 和資料磁片提供磁片區加密。 它也與 Azure Key Vault 整合，可讓我們控制及管理磁片加密金鑰和秘密，並確保 VM 磁片上的所有資料在 Azure 儲存體時都會加密。

- [如何針對 Azure 資料總管叢集啟用靜態加密](https://docs.microsoft.com/azure/data-explorer/manage-cluster-security)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器搭配 Azure 活動記錄檔，以在 azure 資料總管叢集上發生資源層級變更時建立警示。

- [如何建立 Azure 活動記錄事件的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

- [如何建立 Azure 活動記錄事件的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：弱點管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導** 方針：使用 (Azure 資訊安全中心提供的安全分數) 的預設風險評等。

- [瞭解 Azure 資訊安全中心安全分數](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導** 方針：使用 Azure Resource Graph 來查詢及探索訂用帳戶中的所有資源。 確保您的租用戶中有適當的 (讀取) 權限，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可透過 Resource Graph 探索傳統 Azure 資源，但強烈建議您從現在開始建立並使用 Azure Resource Manager 資源。

- [如何使用 Azure Resource Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [如何檢視您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。

- [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導** 方針：您可以適當地使用適當的命名慣例、標記、管理群組或個別訂用帳戶來組織和追蹤資產。 您可以使用 Azure Resource Graph 定期協調清查，並確保及時從訂用帳戶中刪除未經授權的資源。 

- [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [如何使用 Azure Resource Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導** 方針：您將需要根據組織的需求，建立已核准的 Azure 資源和已核准的計算資源軟體的清查。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：您可以使用 Azure 原則來限制可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型：

- 不允許的資源類型

- 允許的資源類型

您將能夠使用可使用 Azure 監視器監視的活動記錄，來監視原則產生的事件。

此外，您可以使用 Azure Resource Graph 來查詢/探索訂用帳戶內的資源。

- [建立和管理原則以強制執行合規性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [快速入門：使用 Azure Resource Graph Explorer 執行您的第一個 Resource Graph 查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [使用 Azure 監視器中建立、檢視及管理活動記錄警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導** 方針：您可以使用 Azure 原則來限制可在客戶訂用帳戶中建立的資源類型， (s) 使用下列內建原則定義：

 - 不允許的資源類型

 - 允許的資源類型

如需詳細資訊，請參閱下列參考資料：

- [建立和管理原則以強制執行合規性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure 原則範例](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：使用 Azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。 這可防止建立和變更 Azure 訂用帳戶中的資源。 

- [使用條件式存取來管理 Azure 管理的存取權](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：使用 Azure 原則別名來建立自訂原則，以對 Azure 資源的設定進行審核或強制執行。 您也可以使用內建的 Azure 原則定義。

此外，Azure Resource Manager 能夠在 JavaScript 物件標記法 (的 JSON) 中匯出範本，您應該檢查這些範本以確保這些設定符合或超過組織的安全性需求。

您也可以使用 Azure 資訊安全中心中的建議作為 Azure 資源的安全設定基準。

- [如何檢視可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [教學課程：建立和管理原則來強制執行相容性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure 入口網站中的單一和多重資源匯出至範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [安全性建議 - 參考指南](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導** 方針：使用 azure 原則 [拒絕] 和 [部署是否不存在]，在您的 Azure 資源上強制執行安全設定。  您可以使用變更追蹤、原則合規性儀表板或自訂解決方案等解決方案，輕鬆地識別環境中的安全性變更。

- [瞭解 Azure 原則效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [建立和管理原則以強制執行合規性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [使用變更追蹤解決方案來追蹤環境中的變更](https://docs.microsoft.com/azure/automation/change-tracking)

- [取得 Azure 資源的合規性資料](https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：使用 Azure Repos 安全地儲存和管理您的程式碼，例如自訂 Azure 原則、Azure Resource Manager 範本、Desired State Configuration 腳本等。若要存取您在 Azure DevOps 中管理的資源，您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD) （如果與 Azure DevOps 整合）中定義的群組，或與 TFS 整合的 Active Directory。

- [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [關於 Azure DevOps 中的許可權和群組](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導** 方針：使用 Azure 原則定義和實行適用于 Azure 資源的標準安全性設定。 使用 Azure 原則別名來建立自訂原則，以對 Azure 資源的網路設定進行審核或強制執行。 您也可以使用與特定資源相關的內建原則定義。  此外，您可以使用 Azure 自動化來部署設定變更。

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用別名](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導** 方針：使用 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 使用 Azure 原則 [audit]、[拒絕] 和 [部署（如果不存在）]，自動強制執行 Azure 資源的設定。

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導** 方針：使用受控識別，在 Azure Active Directory (Azure AD) 中為 Azure 服務提供自動管理的身分識別。 受控識別可供對支援 Azure AD 驗證的任何服務進行驗證 (包括 Key Vault)，不需要程式碼中的任何認證。

- [如何設定受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

- [設定 Azure 資料總管叢集的受控識別](https://docs.microsoft.com/azure/data-explorer/managed-identities)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。 

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [Azure 安全性基準測試：惡意程式碼防護](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導** 方針：支援 azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼 (例如，azure 資料總管) ，但不會在客戶內容上執行。

預先掃描即將上傳至非計算 Azure 資源的任何內容，例如 Azure 資料總管、Data Lake Storage、Blob 儲存體、適用於 PostgreSQL 的 Azure 資料庫等等。Microsoft 無法在這些實例中存取您的資料。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確定定期自動備份

**指導** 方針：您的 Azure 資料總管叢集所使用 Microsoft Azure 儲存體帳戶中的資料一律會進行複寫，以確保持久性和高可用性。 「Azure 儲存體」會複製您的資料，以保護該資料不受計劃性和非計劃性事件影響，包括暫時性硬體故障、網路或電力中斷和大規模天然災害。 您可以選擇在相同資料中心內、在相同地區的不同資料中心內、或跨不同區域複寫您的資料。

- [瞭解 Azure 儲存體的冗余和 Service-Level 協定](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

- [將資料匯出至儲存體](https://docs.microsoft.com/azure/kusto/management/data-export/export-data-to-storage)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導** 方針： Azure 資料總管會加密儲存體帳戶中的所有資料。 根據預設，資料是以使用 Microsoft 管理的金鑰加密。 若要進一步控制加密金鑰，您可以提供客戶管理的金鑰，以用於資料加密。 客戶管理的金鑰必須儲存在 Azure Key Vault 中。

- [使用 Azure 入口網站設定客戶管理的金鑰 ](https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-portal)

- [使用 C 設定客戶管理的金鑰# ](https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp)

- [使用 Azure Resource Manager 範本設定客戶管理的金鑰](https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager)

- [如何備份 Azure Key Vault 憑證](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-4.8.0&amp;preserve-view=true)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：定期測試 Azure Key Vault 秘密的資料還原。

- [如何還原 Azure Key Vault 憑證](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-4.8.0&amp;preserve-view=true)

- [使用 C 設定客戶管理的金鑰# ](https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp)

- [使用 Azure Resource Manager 範本設定客戶管理的金鑰](https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導** 方針：客戶可在 Key Vault 中啟用 Soft-Delete，以防止遭到意外或惡意刪除的金鑰。  您也可以啟用清除保護，如此一來，如果保存庫或物件處於已刪除狀態，則在達到保留期限之前無法予以清除。  

- [如何在 Key Vault 中啟用 Soft-Delete 和清除保護](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

- [使用 C 設定客戶管理的金鑰# ](https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp)

- [使用 Azure Resource Manager 範本設定客戶管理的金鑰](https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。
    

- [ 建立您自己的安全性事件回應流程的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

    

- [ Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

    

- [ 客戶也可以利用 NIST 的電腦安全性性事件處理指南來協助建立自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。

此外，使用標記清楚地標示訂用帳戶 (例如， 生產、非生產) 並建立命名系統，以清楚地識別及分類 Azure 資源，尤其是處理敏感性資料的資源。  您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [使用標記來組織 Azure 資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能，進而協助保護您的 Azure 資源。 找出弱點和落差，並視需要修訂計畫。
    

- [ 請參閱 NIST 的發行集：適用于 IT 方案和功能的測試、訓練和練習程式指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。 事後檢討事件，確保問題已解決。
    

- [如何設定 Azure 資訊安全中心的安全性連絡人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心警示和建議，協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Azure Sentinel。
    

- [ 如何設定連續匯出](https://docs.microsoft.com/azure/security-center/continuous-export)

    

- [ 如何將警示串流至 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導** 方針：使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」安全性警示和建議來自動觸發回應，以保護您的 Azure 資源。
    

- [ 如何設定工作流程自動化和 Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 Red Team 練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導** 方針：遵循 Microsoft Cloud 滲透測試的參與規則，以確保您的滲透測試不違反 Microsoft 原則。 針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。 

- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性基準測試 V2 總覽](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)
