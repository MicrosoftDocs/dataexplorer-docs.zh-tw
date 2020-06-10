---
title: 主體和身分識別提供者-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的主體和身分識別提供者。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4e34c724799cffe38db93869e96fcfae83a92b55
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664988"
---
# <a name="principals-and-identity-providers"></a>主體和身分識別提供者

Kusto 授權模型支援數個識別提供者（Idp）和多個主體類型。
本文將探討支援的主體類型，並示範它們如何搭配[角色指派命令](../../management/security-roles.md)使用。

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory （AAD）是 Azure 慣用的多租使用者雲端目錄服務和身分識別提供者，能夠驗證安全性主體，或與其他身分識別提供者（例如 Microsoft 的 Active Directory）聯盟。

AAD 是向 Kusto 進行驗證的慣用方法。 其支援數種驗證案例：
* **使用者驗證** (互動式登入)：用來驗證人為主體。
* **應用程式驗證 (非互動式登入)** ：用來驗證必須執行/驗證而不存在任何人為使用者的服務和應用程式。

> [!NOTE]
> Azure Active Directory 不允許驗證服務帳戶（也就是內部部署 AD 實體的定義）。
AAD 對等的 AD 服務帳戶是 AAD 應用程式。

#### <a name="aad-group-principals"></a>AAD 群組主體
Kusto 僅支援安全性群組主體（而非發佈群組）。 嘗試在 Kusto 叢集上設定 DG 的存取將會導致錯誤。

#### <a name="aad-tenants"></a>AAD 租使用者

如果未明確指定 AAD 租使用者，則 Kusto 會嘗試從 UPN （例如，）（如）來解析它（ `johndoe@fabrikam.com` 如果有提供的話）。 如果您的主體未包含租使用者資訊（不是 UPN 格式），您必須將租使用者識別碼或名稱附加至主體描述元，以明確提及它。

**AAD 主體的範例**

|AAD 租使用者 |類型 |語法 |
|-----------|-----|-------|
|隱含（UPN）  |User  |`aaduser=`*UserEmailAddress*
|明確（識別碼）   |User  |`aaduser=`*UserEmailAddress* `;`*Tenantid*或 `aaduser=` *ObjectID* `;` *tenantid*
|明確（名稱） |User  |`aaduser=`*UserEmailAddress* `;`*TenantName*或 `aaduser=` *ObjectID* `;` *TenantName*
|隱含（UPN）  |群組 |`aadgroup=`*GroupEmailAddress*
|明確（識別碼）   |群組 |`aadgroup=`*G i d* `;`*Tenantid*或 `aadgroup=` *GroupDisplayName* `;` *tenantid*
|明確（名稱） |群組 |`aadgroup=`*G i d* `;`*TenantName*或 `aadgroup=` *GroupDisplayName* `;` *TenantName*
|Explicit （UPN）  |App   |`aadapp`=*ApplicationDisplayName* `;`*TenantId*
|明確（名稱） |App   |`aadapp=`*ApplicationId* `;`*TenantName*

```kusto
// No need to specify AAD tenant for UPN, as Kusto performs the resolution by itself
.add database Test users ('aaduser=imikeoein@fabrikam.com') 'Test user (AAD)'

// AAD SG on 'fabrikam.com' tenant
.add database Test users ('aadgroup=SGDisplayName;fabrikam.com') 'Test group @fabrikam.com (AAD)'

// AAD App on 'fabrikam.com' tenant - by tenant name
.add database Test users ('aadapp=4c7e82bd-6adb-46c3-b413-fdd44834c69b;fabrikam.com') 'Test app @fabrikam.com (AAD)'
```

### <a name="microsoft-accounts-msas"></a>Microsoft 帳戶 (MSA)
Microsoft 帳戶 (MSA) 一詞用於所有 Microsoft 管理的非組織使用者帳戶 (例如 `hotmail.com`、`live.com`、`outlook.com`)。
Kusto 支援 MSM 的使用者驗證 (請注意，不具安全性群組概念)，這是由其 UPN (通用主體名稱) 所識別。
在 Kusto 資源上設定 MSA 主體時，Kusto **不會**嘗試解析所提供的 UPN。

**MSA 主體的範例**

|IdP  |類型  |語法 |
|-----|------|-------|
|Live.com |User  |`msauser=`john.doe@live.com`

```kusto
.add database Test users ('msauser=john.doe@live.com') 'Test user (live.com)'
```

