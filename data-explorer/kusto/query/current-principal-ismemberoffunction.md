---
title: current_principal_is_member_of （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 current_principal_is_member_of （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4b6f7d0b9ab4074f16ca00b4a3febb1a17351736
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737754"
---
# <a name="current_principal_is_member_of"></a>current_principal_is_member_of()

::: zone pivot="azuredataexplorer"

檢查目前執行查詢之主體的群組成員資格或主體身分識別。

```kusto
print current_principal_is_member_of(
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    )
```

**語法**

`current_principal_is_member_of`(`*list of string literals*`)

**引數**

* *運算式清單*-字串常值的逗號分隔清單，其中每個常值都是主體的完整名稱（FQN）字串，格式如下：  
*PrinciplaType*`=`*PrincipalId*PrincipalId`;`*TenantId*

| PrincipalType   | FQN 前置詞  |
|-----------------|-------------|
| AAD 使用者        | `aaduser=`  |
| AAD 群組       | `aadgroup=` |
| AAD 應用程式 | `aadapp=`   |

**傳回**

此函式會傳回：
* `true`：如果目前執行查詢的主體成功符合至少一個輸入引數。
* `false`：如果執行查詢的目前主體不是任何`aadgroup=` FQN 引數的成員，而且不等於任何`aaduser=`或`aadapp=` FQN 引數。
* `(null)`：如果執行查詢的目前主體不是任何`aadgroup=` FQN 引數的成員，而且不等於任何`aaduser=`或`aadapp=` FQN 引數，而且至少有一個 FQN 引數未成功解析（在 AAD 中未 presed）。 

> [!NOTE]
> 因為此函式會傳回三個狀態的`true`值`false`（、 `null`和），所以請務必只依賴正傳回值來確認成功的成員資格。 換句話說，下列運算式並不相同：
> 
> * `where current_principal_is_member_of('non-existing-group')`
> * `where current_principal_is_member_of('non-existing-group') != false` 


**範例**

```kusto
print result=current_principal_is_member_of(
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    )
```

| result |
|--------|
| (Null) |

使用動態陣列而不是 multple 引數：

```kusto
print result=current_principal_is_member_of(
    dynamic([
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    ]))
```

| result |
|--------|
| (Null) |

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
