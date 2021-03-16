---
title: 適用于 Azure 資料瀏覽器的 azure 原則法規合規性控制項
description: 列出適用于 Azure 資料瀏覽器的 Azure 原則法規合規性控制。 這些內建原則定義提供管理 Azure 資源合規性的常見方法。
ms.date: 03/10/2021
ms.topic: sample
author: orspod
ms.author: orspodek
ms.service: data-explorer
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: 359fea754419ec6df7fa54aedc7fa822af228158
ms.sourcegitcommit: 4ec122b5142e57468b44aa06e0d6522d6dbd9868
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2021
ms.locfileid: "103565705"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-data-explorer"></a>適用于 Azure 資料瀏覽器的 azure 原則法規合規性控制項

[Azure 原則中的法規合規性](/azure/governance/policy/concepts/regulatory-compliance)可針對與不同合規性標準相關的 **合規性網域** 和 **安全性控制**，提供 Microsoft 建立和管理的方案定義 (稱為「內建」)。 此頁面會列出 Azure 資料瀏覽器的 **合規性網域** 和 **安全性控制項** 。 您可以針對 **安全性控制** 個別指派內建項目，以協助讓您的 Azure 資源符合特定標準的規範。

每個內建原則定義的標題都會連結到 Azure 入口網站中的原則定義。 使用 [原則版本] 資料行中的連結來查看 [Azure 原則 GitHub 存放庫](https://github.com/Azure/azure-policy)上的來源。

> [!IMPORTANT]
> 下列每個控制措施都與一或多個 [Azure 原則](/azure/governance/policy/overview)定義相關聯。 這些原則可協助您使用工具[存取合規性](/azure/governance/policy/how-to/get-compliance-data)；不過，控制措施和一或多個原則之間，通常不是一對一或完整對應。 因此，Azure 原則中的 **符合規範** 只是指原則本身，這不保證您符合控制措施所有需求的規範。 此外，合規性標準包含目前未由任何 Azure 原則定義解決的控制措施。 因此，Azure 原則中的合規性只是整體合規性狀態的部分觀點。 這些合規性標準的控制與 Azure 原則法規合規性定義之間的關聯，可能會隨著時間而改變。

## <a name="cmmc-level-3"></a>CMMC 層級3

若要查看適用于所有 Azure 服務的可用 Azure 原則內建如何對應到此合規性標準，請參閱 [Azure 原則法規合規性-CMMC 層級 3](/azure/governance/policy/samples/cmmc-l3)。 如需此合規性標準的詳細資訊，請參閱 [網路安全性成熟度模型認證 (CMMC) ](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)。

|網域 |控制識別碼 |控制標題 |原則<br /><sub>(Azure 入口網站)</sub> |原則版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|系統與通訊保護 |SC. 3.177 |使用 FIPS 驗證的密碼編譯來保護 CUI 的機密性。 |[Azure 資料總管待用加密應使用客戶自控金鑰](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|系統與通訊保護 |SC. 3.177 |使用 FIPS 驗證的密碼編譯來保護 CUI 的機密性。 |[應該在 Azure 資料總管上啟用磁碟加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|系統與通訊保護 |SC. 3.177 |使用 FIPS 驗證的密碼編譯來保護 CUI 的機密性。 |[應該在 Azure 資料總管上啟用雙重加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|系統與通訊保護 |SC. 3.191 |保護待用 CUI 的機密性。 |[應該在 Azure 資料總管上啟用磁碟加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|系統與通訊保護 |SC. 3.191 |保護待用 CUI 的機密性。 |[應該在 Azure 資料總管上啟用雙重加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 原則法規合規性](/azure/governance/policy/concepts/regulatory-compliance)。
- 請參閱 [Azure 原則 GitHub 存放庫](https://github.com/Azure/azure-policy)上的內建項目。
