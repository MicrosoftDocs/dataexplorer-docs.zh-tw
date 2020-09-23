---
title: 安全性角色管理-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的安全性角色管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4bda122b589e3ba297b3e7c350d15687da6ee123
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91056997"
---
# <a name="security-roles-management"></a>安全性角色管理

> [!IMPORTANT]
> 在 Kusto 叢集 (s) 上改變授權規則之前，請先閱讀下列內容： [Kusto 存取控制總覽](../management/access-control/index.md)以  
>  [角色為基礎的授權](../management/access-control/role-based-authorization.md) 

本文說明用來管理安全性角色的控制項命令。
安全性角色會定義哪些安全性主體 (使用者和應用程式) 有權在安全的資源（例如資料庫或資料表）上操作，以及允許的作業。 例如，具有 `database viewer` 特定資料庫安全性角色的主體可以查詢和查看該資料庫的所有實體 (但限制資料表) 除外。

安全性角色可以與安全性主體或安全性群組相關聯， (可以有其他安全性主體或其他安全性群組) 。 當安全性主體嘗試在受保護的資源上進行作業時，系統會檢查主體是否與至少一個安全性角色相關聯，此角色會授與在資源上執行這項作業的許可權。 這稱為 **授權檢查**。 失敗的授權檢查會中止作業。

**語法**

安全性角色管理命令的語法：

*動詞* *SecurableObjectType* *SecurableObjectName* *Role* [ `(` *ListOfPrincipals* `)` [*Description*]]

* *動詞* 命令表示要執行的動作類型： `.show` 、 `.add` 、 `.drop` 和 `.set` 。

    |*動詞命令* |描述                                  |
    |-------|---------------------------------------------|
    |`.show`|傳回目前的值或值。         |
    |`.add` |將一或多個主體新增至角色。     |
    |`.drop`|移除角色中的一或多個主體。|
    |`.set` |將角色設定為特定主體清單，移除所有先前的主體 (如果有任何) 。|

* *SecurableObjectType* 是指定其角色的物件類型。

    |*SecurableObjectType*|描述|
    |---------------------|-----------|
    |`database`|指定的資料庫|
    |`table`|指定的資料表|
    |`materialized-view`| 指定的 [具體化視圖](materialized-views/materialized-view-overview.md)| 

* *SecurableObjectName* 是物件的名稱。

* *Role* 是相關角色的名稱。

    |*角色*      |描述|
    |------------|-----------|
    |`principals`|只能出現為動詞的一部分; 會傳回 `.show` 可能影響安全物件的主體清單。|
    |`admins`    |擁有安全物件的控制權，包括能夠加以查看、修改，以及移除物件和所有子物件。|
    |`users`     |可以查看安全物件，並在其下建立新的物件。|
    |`viewers`   |可以查看安全物件。|
    |`unrestrictedviewers`|只有在資料庫層級上，才允許查看受限制的資料表 (不會公開至「正常」 `viewers` 和 `users`) 。|
    |`ingestors` |在資料庫層級，允許將資料內嵌到所有資料表中。|
    |`monitors`  ||

* *ListOfPrincipals* 是以逗號分隔的選擇性安全性主體識別碼清單， () 類型的值 `string` 。

* [*描述*] 是一種選擇性的值， `string` 會與關聯儲存在一起，以供未來的進行審核之用。

## <a name="example"></a>範例

下列控制命令會列出對資料庫中資料表具有部分存取權的所有安全性主體 `StormEvents` ：

```kusto
.show table StormEvents principals
```

以下是此命令可能產生的結果：

|角色 |PrincipalType |PrincipalDisplayName |PrincipalObjectId |PrincipalFQN 
|---|---|---|---|---
|資料庫 Apsty 管理員 |Azure AD 使用者 |Mark Smith |cd709aed-a26c-e3953dec735e |aaduser =msmith@fabrikam.com|





## <a name="managing-database-security-roles"></a>管理資料庫安全性角色

`.set``database` *DatabaseName* *角色* `none` [ `skip-results` ]

`.set``database` *DatabaseName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.add``database` *DatabaseName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.drop``database` *DatabaseName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

第一個命令會移除角色中的所有主體。 第二個會移除角色中的所有主體，並設定一組新的主體。 第三個會將新的主體新增至角色，而不會移除現有的主體。 最後，會從角色中移除指定的主體，並保留其他角色。

其中：

* *DatabaseName* 是正在修改其安全性角色的資料庫名稱。

* *Role* 為： `admins` 、、、 `ingestors` `monitors` `unrestrictedviewers` 、 `users` 或 `viewers` 。

* *主體* 是一或多個主體。 如需如何指定這些主體的詳細 [資訊，請參閱主體和身分識別提供者](./access-control/principals-and-identity-providers.md) 。

* `skip-results`如果有提供，要求命令不會傳回資料庫主體的更新清單。

* *描述*（如有提供）是將與變更相關聯的文字，並由對應的 `.show` 命令抓取。

<!-- TODO: Need more examples for the public syntax. Until then we're keeping this internal -->


## <a name="managing-table-security-roles"></a>管理資料表安全性角色

`.set``table` *TableName* *Role* `none` [ `skip-results` ]

`.set``table` *TableName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.add``table` *TableName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.drop``table` *TableName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

第一個命令會移除角色中的所有主體。 第二個會移除角色中的所有主體，並設定一組新的主體。 第三個會將新的主體新增至角色，而不會移除現有的主體。 最後，會從角色中移除指定的主體，並保留其他角色。

其中：

* *TableName* 是正在修改其安全性角色的資料表名稱。

* *角色* 為： `admins` 或 `ingestors` 。

* *主體* 是一或多個主體。 如需如何指定這些主體的詳細 [資訊，請參閱主體和身分識別提供者](./access-control/principals-and-identity-providers.md) 。

* `skip-results`如果有提供，要求命令不會傳回資料表主體的更新清單。

* *描述*（如有提供）是將與變更相關聯的文字，並由對應的 `.show` 命令抓取。

**範例**

```kusto
.add table Test admins ('aaduser=imike@fabrikam.com ')
```

## <a name="managing-materialized-view-security-roles"></a>管理具體化視圖安全性角色

`.show``materialized-view` *MaterializedViewName*`principals`

`.set``materialized-view` *MaterializedViewName* `admins` MaterializedViewName `(`*主體* `,[`*主體 ...*`])`

`.add``materialized-view` *MaterializedViewName* `admins` MaterializedViewName `(`*主體* `,[`*主體 ...*`])`

`.drop``materialized-view` *MaterializedViewName* `admins` MaterializedViewName `(`*主體* `,[`*主體 ...*`])`

其中：

* *MaterializedViewName* 是要修改其安全性角色的具體化視圖名稱。
* *主體* 是一或多個主體。 查看 [主體和身分識別提供者](./access-control/principals-and-identity-providers.md)

## <a name="managing-function-security-roles"></a>管理函數安全性角色

`.set``function` *FunctionName* *角色* `none` [ `skip-results` ]

`.set``function` *FunctionName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.add``function` *FunctionName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

`.drop``function` *FunctionName* *Role* `(` *Principal* [ `,` *principal*...] `)` [ `skip-results` ] [*Description*]

第一個命令會移除角色中的所有主體。 第二個會移除角色中的所有主體，並設定一組新的主體。 第三個會將新的主體新增至角色，而不會移除現有的主體。 最後，會從角色中移除指定的主體，並保留其他角色。

其中：

* *FunctionName* 是要修改其安全性角色的函式名稱。

* *角色* 一律為 `admin` 。

* *主體* 是一或多個主體。 如需如何指定這些主體的詳細 [資訊，請參閱主體和身分識別提供者](./access-control/principals-and-identity-providers.md) 。

* `skip-results`如果有提供，要求命令不會傳回已更新的函式主體清單。

* *描述*（如有提供）是將與變更相關聯的文字，並由對應的 `.show` 命令抓取。

**範例**

```kusto
.add function MyFunction admins ('aaduser=imike@fabrikam.com') 'This user should have access'
```

