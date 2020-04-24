---
title: 安全角色管理 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的安全角色管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea56911ff2ad320d1070da2a4b92d94f060273cf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520089"
---
# <a name="security-roles-management"></a>安全角色管理

> [!IMPORTANT]
> 在變更 Kusto 叢集上的授權規則之前,請閱讀以下內容:[基於 Kusto 存取控制概述](../management/access-control/index.md) 
> [角色的授權](../management/access-control/role-based-authorization.md) 

本文介紹了用於管理安全角色的控制命令。
安全角色定義哪些安全主體(用戶和應用程式)有權對安全資源(如資料庫或表)進行操作,以及允許執行哪些操作。 例如,具有特定資料庫`database viewer`安全角色的主體可以查詢和查看該資料庫的所有實體(受限表除外)。

安全角色可以與安全主體或安全組(可以具有其他安全主體或其他安全組)相關聯。 當安全主體嘗試對安全資源執行操作時,系統會檢查主體是否與至少一個授予對資源執行此操作許可權的安全角色相關聯。 這稱為**授權檢查**。 授權檢查失敗將中止操作。

**語法**

安全角色管理命令的語法:

*動詞**可保護物件類型**可安全物件名稱**角色*`(`[*主體*`)`清單 »*描述*]

* *沒有設定字*`.show`型指示要執行的操作型`.add`態`.drop`:`.set`, 與與 。

    |*動詞* |描述                                  |
    |-------|---------------------------------------------|
    |`.show`|返回當前值或值。         |
    |`.add` |向角色添加一個或多個主體。     |
    |`.drop`|從角色中刪除一個或多個主體。|
    |`.set` |將角色設置到主體的特定清單,刪除所有以前的主體(如果有)。|

* *Secur 可物件類型*是指定其角色的物件類型。

    |*可安全物件類型*|描述|
    |---------------------|-----------|
    |`database`|指定的資料庫|
    |`table`|指定的表|

* *可安全物件名稱*是對象的名稱。

* *角色*是相關角色的名稱。

    |*角色*      |描述|
    |------------|-----------|
    |`principals`|只能作為動詞的一`.show`部分出現;返回可能影響可保護物件的主體清單。|
    |`admins`    |控制可保護物件,包括查看、修改物件和刪除物件和所有子物件的能力。|
    |`users`     |可以查看可保護物件,並在其下方創建新物件。|
    |`viewers`   |可以查看可保護物件。|
    |`unrestrictedviewers`|僅在資料庫級別,允許查看受限表(這些表不會暴露給"正常"`viewers``users`和 )。|
    |`ingestors` |僅在資料庫級別,允許將數據引入所有表。|
    |`monitors`  ||

* *Listofis*是安全主體識別碼(`string`類型值)的可選、逗號分隔清單。

* *描述*是與關聯一起`string`存儲的可選類型值,用於將來的審核目的。

## <a name="example"></a>範例

以下控制命令列出了對資料庫中表`StormEvents`具有某種訪問許可權的所有安全主體:

```kusto
.show table StormEvents principals
```

以下是此指令的潛在結果:

|角色 |PrincipalType |主體顯示名稱 |主體物件Id |本金 
|---|---|---|---|---
|資料庫 Apsty 管理員 |AAD 使用者 |馬克·史密斯 |cd709aed-a26c-e3953dec735e |亞達瑟。msmith@fabrikam.com|





## <a name="managing-database-security-roles"></a>管理資料庫安全角色

`.set``database`*資料庫名稱**角色*`none``skip-results`|

`.set``database`*資料庫名稱**角色*`(`*主體*`,`=*主體*...`)` [`skip-results`] [ ]*描述*】

`.add``database`*資料庫名稱**角色*`(`*主體*`,`=*主體*...`)` [`skip-results`] [ ]*描述*】

`.drop``database`*資料庫名稱**角色*`(`*主體*`,`=*主體*...`)` [`skip-results`] [ ]*描述*】

第一個命令從角色中刪除所有主體。 第二個主體從角色中刪除所有主體,並設置一組新的主體。 第三個在不刪除現有主體的情況下向角色添加新主體。 最後一個從角色中刪除指示的主體,並保留其他主體。

其中：

* *資料庫名稱*是正在修改其安全角色的資料庫的名稱。

* *角色*為`admins``ingestors`: `monitors` `unrestrictedviewers` `users` `viewers` 、、、、、、、、、、、、、、、、、、、、、、、、、、、、、

* *校長*是一個或多個主體。 有關如何指定這些主體,請參閱[主體和標識提供者](./access-control/principals-and-identity-providers.md)。

* `skip-results`,如果提供,請求該命令不會返回資料庫主體的更新清單。

* *說明*(如果提供)是與更改關聯的文本,並由`.show`相應的 命令檢索。

<!-- TODO: Need more examples for the public syntax. Until then we're keeping this internal -->


## <a name="managing-table-security-roles"></a>管理表安全角色

`.set``table`*表名**角色*`none``skip-results`|

`.set``table`*表格**名稱 角色*`(`*主體*`,`=*主體*...`)` [`skip-results`] [ ]*描述*】

`.add``table`*表格**名稱 角色*`(`*主體*`,`=*主體*...`)` [`skip-results`] [ ]*描述*】

`.drop``table`*表格**名稱 角色*`(`*主體*`,`=*主體*...`)` [`skip-results`] [ ]*描述*】

第一個命令從角色中刪除所有主體。 第二個主體從角色中刪除所有主體,並設置一組新的主體。 第三個在不刪除現有主體的情況下向角色添加新主體。 最後一個從角色中刪除指示的主體,並保留其他主體。

其中：

* *表名稱*是正在修改其安全角色的表的名稱。

* *角色*是`admins``ingestors`: 或 。

* *校長*是一個或多個主體。 有關如何指定這些主體,請參閱[主體和標識提供者](./access-control/principals-and-identity-providers.md)。

* `skip-results`如果提供,則請求該命令不會返回更新的表主體清單。

* *說明*(如果提供)是與更改關聯的文本,並由`.show`相應的 命令檢索。

**範例**

```kusto
.add table Test admins ('aaduser=imike@fabrikam.com ')
```

## <a name="managing-function-security-roles"></a>管理功能安全角色

`.set``function`*函數名稱**角色*`none``skip-results`|

`.set``function`*函數名稱**角色*`(`*主體*`,`=*主體*...`)` [`skip-results`] [ ]*描述*】

`.add``function`*函數名稱**角色*`(`*主體*`,`=*主體*...`)` [`skip-results`] [ ]*描述*】

`.drop``function`*函數名稱**角色*`(`*主體*`,`=*主體*...`)` [`skip-results`] [ ]*描述*】

第一個命令從角色中刪除所有主體。 第二個主體從角色中刪除所有主體,並設置一組新的主體。 第三個在不刪除現有主體的情況下向角色添加新主體。 最後一個從角色中刪除指示的主體,並保留其他主體。

其中：

* *函數名稱*是正在修改其安全角色的函數的名稱。

* *角色*`admin`總是 。

* *校長*是一個或多個主體。 有關如何指定這些主體,請參閱[主體和標識提供者](./access-control/principals-and-identity-providers.md)。

* `skip-results`,如果提供,請求該命令不會返回更新的函數主體清單。

* *說明*(如果提供)是與更改關聯的文本,並由`.show`相應的 命令檢索。

**範例**

```kusto
.add function MyFunction admins ('aaduser=imike@fabrikam.com') 'This user should have access'
```

