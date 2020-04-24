---
title: 主體和標識提供者 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的主體和標識提供程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a0638ba0031162dadbb4b9a2815940e66d4dcfb3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522639"
---
# <a name="principals-and-identity-providers"></a>主體與身份提供者

Kusto 授權模型支援多個識別提供者 (IdP) 和多個主體類型。
本文回顧了受支持的主體類型,並演示了它們與[角色分配命令](../../management/security-roles.md)的用。

### <a name="azure-active-directory"></a>Azure Active Directory
Azure 活動目錄 (AAD) 是 Azure 首選的多租戶雲目錄服務和標識提供者,能夠驗證安全主體或與其他標識提供者(如 Microsoft 的活動目錄)聯合。

AAD 是驗證庫塞托的首選方法。 其支援數種驗證案例：
* **使用者驗證** (互動式登入)：用來驗證人為主體。
* **應用程式驗證 (非互動式登入)** ：用來驗證必須執行/驗證而不存在任何人為使用者的服務和應用程式。

>注: Azure 活動目錄不允許對服務帳戶進行身份驗證(根據定義,在 AD 實體上)。
AD 服務帳戶的 AAD 等效項是 AAD 應用程式。

#### <a name="aad-group-principals"></a>AAD 集團負責人
Kusto 僅支援安全組主體(而不是通訊組主體)。 嘗試在 Kusto 群集上設定 DG 的訪問將導致錯誤。

#### <a name="aad-tenants"></a>AAD 租戶


>如果未顯式指定 AAD 租戶,則 Kusto 將嘗試從`johndoe@fabrikam.com`UPN(例如, 通用主名)解析它(如果提供)。
如果您的主體不包含租戶資訊(不在 UPN 窗體中),則必須通過將租戶 ID 或名稱追加到主體描述符來顯式提及這些資訊。

**AAD 主體範例**

|AAD 租戶 |類型 |語法 |
|-----------|-----|-------|
|隱含 (UPN)  |User  |`aaduser=`*使用者電子郵件地址*
|明確 (ID)   |User  |`aaduser=`*使用者電子郵件地址*`;`*租戶 Id*`aaduser=`或*ObjectID*`;`*租戶 Id*
|明確(名稱) |User  |`aaduser=`*使用者電子郵件地址*`;`*租戶名稱*或`aaduser=` *ObjectID*`;`*租戶名稱*
|隱含 (UPN)  |群組 |`aadgroup=`*叢發位址*
|明確 (ID)   |群組 |`aadgroup=`*群組 ObjectId*`;`*租戶 Id*或`aadgroup=`*組元顯示名稱*`;`*租戶 Id*
|明確(名稱) |群組 |`aadgroup=`*群組 Objectid*`;`*租戶名稱*或`aadgroup=`*群組顯示名稱*`;`*租戶名稱*
|明確 (UPN)  |App   |`aadapp`=*應用程式顯示名稱*`;`*租戶 Id*
|明確(名稱) |App   |`aadapp=`*應用程式 Id*`;`*租戶名稱*

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

**MSA 主體範例**

|IdP  |類型  |語法 |
|-----|------|-------|
|Live.com |User  |`msauser=`john.doe@live.com`

```kusto
.add database Test users ('msauser=john.doe@live.com') 'Test user (live.com)'
```

