---
title: 適用于 Azure 資料總管的內建原則定義
description: 列出 Azure 資料總管 Azure 原則內建原則定義。 這些內建原則定義提供管理 Azure 資源的常見方法。
ms.date: 11/17/2020
ms.topic: reference
author: orspod
ms.author: orspodek
ms.service: data-explorer
ms.custom: subject-policy-reference
ms.openlocfilehash: 9f2e98a1be83be659c8f7b85283c868d96f999eb
ms.sourcegitcommit: 0820454feb02ae489f3a86b688690422ae29d788
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94937916"
---
# <a name="azure-policy-built-in-definitions-for-azure-data-explorer"></a>適用于 Azure 資料總管的 Azure 原則內建定義

此頁面是 Azure 資料總管 [Azure 原則](/azure/governance/policy/overview) 內建原則定義的索引。 如需其他服務的其他內建 Azure 原則，請參閱 [Azure 原則內建定義](/azure/governance/policy/samples/built-in-policies)。

連結至 Azure 入口網站中原則定義的每個內建原則定義名稱。 使用 [版本] 資料行中的連結來查看 [Azure 原則 GitHub 存放庫](https://github.com/Azure/azure-policy)上的來源。

## <a name="azure-data-explorer"></a>Azure 資料總管

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 資料總管靜止加密應該使用客戶管理的金鑰](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |在您的 Azure 資料總管叢集上使用客戶管理的金鑰啟用待用加密，可讓您額外控制待用加密所使用的金鑰。 這項功能通常適用于具有特殊合規性需求的客戶，而且需要 Key Vault 來管理金鑰。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[應該在 Azure 資料總管上啟用磁片加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |啟用磁片加密可協助保護您的資料，以符合組織的安全性和合規性承諾。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[應該在 Azure 資料總管上啟用雙重加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |啟用雙重加密可協助保護您的資料，以符合組織的安全性和合規性承諾。 啟用雙重加密時，儲存體帳戶中的資料會加密兩次，一次是在服務層級進行，一次在基礎結構層級，使用兩個不同的加密演算法和兩個不同的金鑰。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[應針對 Azure 資料總管啟用虛擬網路插入](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |使用虛擬網路插入保護網路周邊的安全，讓您能夠強制執行網路安全性群組規則、連接內部部署，並使用服務端點保護您的資料連線來源。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 原則 GitHub 存放庫](https://github.com/Azure/azure-policy)上的內建項目。
- 檢閱 [Azure 原則定義結構](/azure/governance/policy/concepts/definition-structure)。
- 檢閱[了解原則效果](/azure/governance/policy/concepts/effects)。
