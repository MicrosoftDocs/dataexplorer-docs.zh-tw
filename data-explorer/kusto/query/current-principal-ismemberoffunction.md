---
title: current_principal_is_member_of() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 azure 數據資源管理器中的current_principal_is_member_of()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
zone_pivot_group_filename: kusto/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 6d33fd19181e1eba93b54f684864fed062701307
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663851"
---
# <a name="current_principal_is_member_of"></a>current_principal_is_member_of()

::: zone pivot="azuredataexplorer"

檢查運行查詢的當前主體的組成員身份或主體標識。

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

* *表示式清單*- 字串文字的逗號分隔清單,其中每個文字都是主體完全限定名稱 (FQN) 字串,形成於:  
*普林西貝拉類型*`=`*主體租戶*`;`*Id*

| PrincipalType   | FQN 首碼  |
|-----------------|-------------|
| AAD 使用者        | `aaduser=`  |
| AAD集團       | `aadgroup=` |
| AAD 應用程式 | `aadapp=`   |

**傳回**

此函式會傳回：
* `true`:如果運行查詢的當前主體已成功匹配至少一個輸入參數。
* `false`:如果運行查詢的當前主體不是任何`aadgroup=`FQN 參數的成員,並且不等`aaduser=`於`aadapp=`任何 或 FQN 參數。
* `(null)`:如果運行查詢的當前主體不是任何`aadgroup=`FQN 參數的成員,並且不等`aaduser=`於`aadapp=`任何 或 FQN 參數,並且至少有一個 FQN 參數未成功解析(未在 AAD 中預置)。 

> [!NOTE]
> 由於函數返回三狀態值`true``false`(、`null`和 ),因此僅依賴正返回值以確認成功成員資格非常重要。 換句話說,以下表達式不同:
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

使用動態陣列而不是多反參數:

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

Azure 監視器不支援此功能

::: zone-end