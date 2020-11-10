---
title: 叢集對等命令-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的叢集對等命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 26412683be35825a38f959de62292f3735e7a894
ms.sourcegitcommit: e820a59191d2ca4394e233d51df7a0584fa4494d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94446220"
---
# <a name="cluster-follower-commands"></a>叢集的對等命令

以下列出用來管理進行中叢集設定的控制命令。 這些命令會以同步方式執行，但會在下一次週期性架構重新整理時套用。 因此，在套用新的設定之前，可能會有幾分鐘的延遲。

這包括 [資料庫層級命令](#database-level-commands) 和 [資料表層級](#table-level-commands)的命令。

## <a name="database-level-commands"></a>資料庫層級命令

### <a name="show-follower-database"></a>。顯示資料的資料庫

顯示一或多個資料庫層級的資料庫， (或資料庫) 接著設定了一或多個資料庫層級的覆寫。

**語法**

`.show``follower` `database` *DatabaseName*

`.show``follower` `databases` `(`*DatabaseName1* `,`...`,`*DatabaseNameN*`)`

**輸出** 

| 輸出參數                     | 類型    | 說明                                                                                                        |
|--------------------------------------|---------|--------------------------------------------------------------------------------------------------------------------|
| DatabaseName                         | String  | 要遵循的資料庫名稱。                                                                           |
| LeaderClusterMetadataPath            | String  | 領導者叢集中繼資料容器的路徑。                                                               |
| CachingPolicyOverride                | String  | 資料庫的覆寫快取原則（序列化為 JSON 或 null）。                                         |
| AuthorizedPrincipalsOverride         | String  | 資料庫的授權主體覆寫集合，序列化為 JSON 或 null。                    |
| AuthorizedPrincipalsModificationKind | String  | 要使用 AuthorizedPrincipalsOverride (或) 套用的修改種類 `none` `union` `replace` 。                  |
| CachingPoliciesModificationKind      | String  | 要使用資料庫或資料表層級快取原則覆寫來套用的修改種類 (`none` ， `union` 或 `replace`) 。 |
| IsAutoPrefetchEnabled                | 布林值 | 每次重新整理架構時是否預先提取新的資料。        |
| TableMetadataOverrides               | String  | 資料表層級屬性覆寫的 JSON 序列化 (（如果定義) ）。                                      |

### <a name="alter-follower-database-policy-caching"></a>。 alter database 原則快取

改變數據的資料庫快取原則，以覆寫領導者叢集中的源資料庫上所設定的原則。 它需要 [DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。

**注意事項**

* 快取原則的預設值 `modification kind` 為 `union` 。 若要變更， `modification kind` 請使用 [. alter 資料庫快取-原則-修改類型](#alter-follower-database-caching-policies-modification-kind) 命令。
* 在變更之後，您可以使用下列命令來查看原則或有效的原則 `.show` ：
    * [。顯示資料庫原則保留期](../management/retention-policy.md#show-retention-policy)
    * [。顯示資料庫詳細資料](../management/show-databases.md)
    * [.show 資料表詳細資料](show-tables-command.md)
* 在變更之後，您可以使用，在資料在進行變更之後，于資料在瀏覽器資料庫上進行覆寫設定[。顯示](#show-follower-database)瀏覽器資料庫

**語法**

`.alter``follower` `database` *DatabaseName* `policy` `caching` `hot` `=` *HotDataSpan*

**範例**

```kusto
.alter follower database MyDb policy caching hot = 7d
```

### <a name="delete-follower-database-policy-caching"></a>. 刪除資料庫策略快取

刪除資料的資料庫覆寫快取原則。 這會使領導者叢集中源資料庫的原則設定成為有效的。
它需要 [DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。 

**注意事項**

* 在變更之後，您可以使用下列命令來查看原則或有效的原則 `.show` ：
    * [。顯示資料庫原則保留期](../management/retention-policy.md#show-retention-policy)
    * [。顯示資料庫詳細資料](../management/show-databases.md)
    * [.show 資料表詳細資料](show-tables-command.md)
* 您可以使用來完成變更後，于資料的資料庫上進行覆寫設定[。顯示](#show-follower-database)流覽的資料庫

**語法**

`.delete``follower` `database` *DatabaseName* DatabaseName `policy``caching`

**範例**

```kusto
.delete follower database MyDB policy caching
```

### <a name="add-follower-database-principals"></a>。加入資料庫主體

將已授權的主體 (s) 新增至覆寫授權主體的「對等」資料庫集合。 它需要 [DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。

**注意事項**

* `modification kind`這類授權主體的預設值為 `none` 。 若要變更 `modification kind` 使用  [alter get-help 資料庫主體-修改類型](#alter-follower-database-principals-modification-kind)。
* 在變更之後，您可以使用下列命令來查看有效的主體集合 `.show` ：
    * [。顯示資料庫主體](../management/security-roles.md#managing-database-security-roles)
    * [。顯示資料庫詳細資料](../management/show-databases.md)
* 您可以使用來完成變更後，于資料的資料庫上進行覆寫設定[。顯示](#show-follower-database)流覽的資料庫

**語法**

`.add``follower` `database` *DatabaseName* (`admins`  |  `users`  |  `viewers`  |  `monitors`) 角色 `(` *principal1* `,` ... `,`*principalN* `)`[ `'` *附注* `'` ]

**範例**

```kusto
.add follower database MyDB viewers ('aadgroup=mygroup@microsoft.com') 'My Group'
```

### <a name="drop-follower-database-principals"></a>。卸載資料庫主體

卸載已授權的主體 (s，) 從覆寫授權的主體的資料從資料庫集合中取得。
它需要 [DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。

**注意事項**

* 在變更之後，您可以使用下列命令來查看有效的主體集合 `.show` ：
    * [。顯示資料庫主體](../management/security-roles.md#managing-database-security-roles)
    * [。顯示資料庫詳細資料](../management/show-databases.md)
* 您可以使用來完成變更後，于資料的資料庫上進行覆寫設定[。顯示](#show-follower-database)流覽的資料庫

**語法**

`.drop``follower` `database` *DatabaseName* (`admins`  |  `users`  |  `viewers`  |  `monitors`) `(` *principal1* `,` ... `,`*principalN*`)`

**範例**

```kusto
.drop follower database MyDB viewers ('aadgroup=mygroup@microsoft.com')
```

### <a name="alter-follower-database-principals-modification-kind"></a>。 alter database 主體-修改類型

改變資料庫授權的主體修改種類。 它需要 [DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。

**注意事項**

* 在變更之後，您可以使用下列命令來查看有效的主體集合 `.show` ：
    * [。顯示資料庫主體](../management/security-roles.md#managing-database-security-roles)
    * [。顯示資料庫詳細資料](../management/show-databases.md)
* 您可以使用來完成變更後，于資料的資料庫上進行覆寫設定[。顯示](#show-follower-database)流覽的資料庫

**語法**

`.alter``follower` `database` *DatabaseName* 
 `principals-modification-kind` = (`none`  |  `union`  |  `replace`) 

**範例**

```kusto
.alter follower database MyDB principals-modification-kind = union
```

### <a name="alter-follower-database-caching-policies-modification-kind"></a>。 alter database 快取-原則-修改-種類

改變數據的資料庫和資料表快取原則的修改種類。 它需要 [DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。

**注意事項**

* 在變更之後，您可以使用標準命令來查看資料庫/資料表層級快取原則的有效集合 `.show` ：
    * [。顯示資料表詳細資料](show-tables-command.md)
    * [。顯示資料庫詳細資料](../management/show-databases.md)
* 您可以使用來完成變更後，于資料的資料庫上進行覆寫設定[。顯示](#show-follower-database)流覽的資料庫

**語法**

`.alter``follower` `database` *DatabaseName* `caching-policies-modification-kind` = (`none`  |  `union`  |  `replace`) 

**範例**

```kusto
.alter follower database MyDB caching-policies-modification-kind = union
```

### <a name="alter-follower-database-prefetch-extents"></a>。 alter 的資料庫預先提取-範圍

在從基礎儲存體提取至節點的 SSD (快取) 之前，從基礎儲存體提取至節點的「SSD」叢集可能不會讓任何新的資料可供查詢。

下列命令會在每次架構重新整理時，改變預先提取新範圍的「進行中」資料庫設定。 它需要 [DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。

> [!WARNING]
> * 啟用此設定可能會降低資料的資料在資料的資料中。
> * 預設設定為 `false` ，建議您保留預設值。
> * 選擇將設定變更為時 `true` ，建議您在設定變更之後，仔細評估對有效期限的影響。

**語法**

`.alter``follower` `database` *DatabaseName* `prefetch-extents` = (`true`  |  `false`) 

`.alter``follower` `database` *DatabaseName* [ `from` `h@'` *領導者叢集的中繼資料容器路徑* `'` ] `prefetch-extents` = (`true`  |  `false`) 

**範例**

<!-- csl -->
```
.alter follower database MyDB prefetch-extents = false
```

## <a name="table-level-commands"></a>資料表層級命令

### <a name="alter-follower-table-policy-caching"></a>。 alter alter table 原則快取

更改對等資料庫的資料表層級快取原則，以覆寫在領導者叢集中的源資料庫上設定的原則。
它需要 [DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。 

**注意事項**

* 在變更之後，您可以使用下列命令來查看原則或有效的原則 `.show` ：
    * [。顯示資料庫原則保留期](../management/retention-policy.md#show-retention-policy)
    * [。顯示資料庫詳細資料](../management/show-databases.md)
    * [.show 資料表詳細資料](show-tables-command.md)
* 您可以使用來完成變更後，于資料的資料庫上進行覆寫設定[。顯示](#show-follower-database)流覽的資料庫

**語法**

`.alter``follower` `database` *DatabaseName* 資料表 *TableName* `policy` `caching` `hot` `=` *HotDataSpan*

`.alter``follower` `database` *DatabaseName* 資料表 `(` *TableName1* `,` ... `,`*TableNameN* `)``policy` `caching` `hot``=` *HotDataSpan*

**範例**

```kusto
.alter follower database MyDb tables (Table1, Table2) policy caching hot = 7d
```

### <a name="delete-follower-table-policy-caching"></a>。刪除進行中的資料表原則快取

刪除對等資料庫上的覆寫資料表層級快取原則，讓領導者叢集中源資料庫的原則設定為有效的。 需要 [DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。 

**注意事項**

* 在變更之後，您可以使用下列命令來查看原則或有效的原則 `.show` ：
    * [。顯示資料庫原則保留期](../management/retention-policy.md#show-retention-policy)
    * [。顯示資料庫詳細資料](../management/show-databases.md)
    * [.show 資料表詳細資料](show-tables-command.md)
* 您可以使用來完成變更後，于資料的資料庫上進行覆寫設定[。顯示](#show-follower-database)流覽的資料庫

**語法**

`.delete``follower` `database` *DatabaseName* `table` *TableName* TableName `policy``caching`

`.delete``follower` `database` *DatabaseName* `tables` `(` *TableName1* `,` ... `,`*TableNameN* `)``policy``caching`

**範例**

```kusto
.delete follower database MyDB tables (Table1, Table2) policy caching
```

## <a name="sample-configuration"></a>範例組態

以下是設定「資料執行者」資料庫的範例步驟。

在此範例中：

* 我們的執行者叢集 `MyFollowerCluster` 將會遵循 `MyDatabase` 領導者叢集的資料庫 `MyLeaderCluster` 。
    * `MyDatabase` 具有 `N` 資料表： `MyTable1` 、 `MyTable2` 、 `MyTable3` 、... `MyTableN` (`N` > 3) 。
    * 在 `MyLeaderCluster`上：
    
    | `MyTable1` 快取原則 | `MyTable2` 快取原則 | `MyTable3`...`MyTableN` 快取原則   | `MyDatabase` 授權的主體                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | 經常性資料範圍 = `7d`      | 經常性資料範圍 = `30d`     | 經常性資料範圍 = `365d`                   | *查看* 器  =  `aadgroup=scubadivers@contoso.com` ;系統 *管理員* = `aaduser=jack@contoso.com` |
     
    * 在 `MyFollowerCluster` 我們想要：
    
    | `MyTable1` 快取原則 | `MyTable2` 快取原則 | `MyTable3`...`MyTableN` 快取原則   | `MyDatabase` 授權的主體                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | 經常性資料範圍 = `1d`      | 經常性資料範圍 = `3d`      | 經常性資料範圍 = `0d` 不 (快取任何內容)  | 系統 *管理員*  =  `aaduser=jack@contoso.com` 、 *查看* 器 = `aaduser=jill@contoso.com`         |

> [!IMPORTANT] 
> `MyFollowerCluster`和都 `MyLeaderCluster` 必須位於相同的區域。

### <a name="steps-to-execute"></a>要執行的步驟

*先決條件：* 設定叢集 `MyFollowerCluster` ，以依照叢集的資料庫執行 `MyDatabase` `MyLeaderCluster` 。

> [!NOTE]
> 執行控制命令的主體應為 `DatabaseAdmin` on 資料庫 `MyDatabase` 。

#### <a name="show-the-current-configuration"></a>顯示目前的設定

請根據下列內容查看目前的設定 `MyDatabase` `MyFollowerCluster` ：

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| 資料行                              | 值                                                    |
|-------------------------------------|----------------------------------------------------------|
|DatabaseName                         | MyDatabase                                               |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster` |
|CachingPolicyOverride                | null                                                     |
|AuthorizedPrincipalsOverride         | []                                                       |
|AuthorizedPrincipalsModificationKind | 無                                                     |
|IsAutoPrefetchEnabled                | 否                                                    |
|TableMetadataOverrides               |                                                          |
|CachingPoliciesModificationKind      | Union                                                    |                                                                                                                      |

#### <a name="override-authorized-principals"></a>覆寫授權的主體

`MyDatabase` `MyFollowerCluster` 使用僅包含一個 aad 使用者做為資料庫管理員，並將一個 aad 使用者作為資料庫檢視器的集合，取代為上的授權主體集合：

```kusto
.add follower database MyDatabase admins ('aaduser=jack@contoso.com')

.add follower database MyDatabase viewers ('aaduser=jill@contoso.com')

.alter follower database MyDatabase principals-modification-kind = replace
```

只有這兩個特定主體有 `MyDatabase` 權存取 `MyFollowerCluster`

```kusto
.show database MyDatabase principals
```

| 角色                       | PrincipalType | PrincipalDisplayName                        | PrincipalObjectId                    | PrincipalFQN                                                                      | 備註 |
|----------------------------|---------------|---------------------------------------------|--------------------------------------|-----------------------------------------------------------------------------------|-------|
| 資料庫 MyDatabase 管理員  | AAD 使用者      | Kusto (upn：) 的插座 jack@contoso.com       | 12345678-abcd-efef-1234-350bf486087b | aaduser = 87654321-abcd-efef-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |       |
| 資料庫 MyDatabase 檢視器 | AAD 使用者      | Jill Kusto (upn： jack@contoso.com)        | abcdefab-abcd-efef-1234-350bf486087b | aaduser = 54321789-abcd-efef-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |       |

```kusto
.show follower database MyDatabase
| mv-expand parse_json(AuthorizedPrincipalsOverride)
| project AuthorizedPrincipalsOverride.Principal.FullyQualifiedName
```

| AuthorizedPrincipalsOverride_Principal_FullyQualifiedName                         |
|-----------------------------------------------------------------------------------|
| aaduser = 87654321-abcd-efef-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |
| aaduser = 54321789-abcd-efef-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |

#### <a name="override-caching-policies"></a>覆寫快取原則

藉 `MyDatabase` `MyFollowerCluster` 由將所有資料表設定為 *不* 將其資料快取（不含兩個特定資料表）來取代的資料庫和資料表層級快取原則的集合， `MyTable1` `MyTable2` 這會將其資料分別快取為和的期間 `1d` `3d` ：

```kusto
.alter follower database MyDatabase policy caching hot = 0d

.alter follower database MyDatabase table MyTable1 policy caching hot = 1d

.alter follower database MyDatabase table MyTable2 policy caching hot = 3d

.alter follower database MyDatabase caching-policies-modification-kind = replace
```

只有這兩個特定的資料表會快取資料，而且資料表的其餘部分會有最常使用的資料期間 `0d` ：

```kusto
.show tables details
| summarize TableNames = make_list(TableName) by CachingPolicy
```
| CachingPolicy                                                                | TableNames                  |
|------------------------------------------------------------------------------|-----------------------------|
| {"DataHotSpan"： {"Value"： "1.00：00： 00"}，"IndexHotSpan"： {"Value"： "1.00：00： 00"}} | ["MyTable1"]                |
| {"DataHotSpan"： {"Value"： "3.00：00： 00"}，"IndexHotSpan"： {"Value"： "3.00：00： 00"}} | ["MyTable2"]                |
| {"DataHotSpan"： {"Value"： "0.00：00： 00"}，"IndexHotSpan"： {"Value"： "0.00：00： 00"}} | ["MyTable3",..., "MyTableN"] |

```kusto
.show follower database MyDatabase
| mv-expand parse_json(TableMetadataOverrides)
| project TableMetadataOverrides
```

| TableMetadataOverrides                                                                                              |
|---------------------------------------------------------------------------------------------------------------------|
| {"MyTable1"： {"CachingPolicyOverride"： {"DataHotSpan"： {"Value"： "1.00：00： 00"}，"IndexHotSpan"： {"Value"： "1.00：00： 00"}}} |
| {"MyTable2"： {"CachingPolicyOverride"： {"DataHotSpan"： {"Value"： "3.00：00： 00"}，"IndexHotSpan"： {"Value"： "3.00：00： 00"}}} |

#### <a name="summary"></a>總結

請參閱目前的設定， `MyDatabase` `MyFollowerCluster` 如下所示：

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| 資料行                              | 值                                                                                                                                                                           |
|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|DatabaseName                         | MyDatabase                                                                                                                                                                      |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster`                                                                                                                        |
|CachingPolicyOverride                | {"DataHotSpan"： {"Value"： "00：00： 00"}，"IndexHotSpan"： {"Value"： "00：00： 00"}}                                                                                                        |
|AuthorizedPrincipalsOverride         | [{"Principal"： {"FullyQualifiedName"： "aaduser = 87654321-abcd-efef-1234-350bf486087b",...}，{"Principal"： {"FullyQualifiedName"： "aaduser = 54321789-abcd-efef-1234-350bf486087b",...}] |
|AuthorizedPrincipalsModificationKind | 取代                                                                                                                                                                         |
|IsAutoPrefetchEnabled                | 否                                                                                                                                                                           |
|TableMetadataOverrides               | {"MyTargetTable"： {"CachingPolicyOverride"： {"DataHotSpan"： {"Value"： "3.00：00： 00"} ...}，"MySourceTable"： {"CachingPolicyOverride"： {"DataHotSpan"： {"Value"： "1.00：00： 00"},...}}}       |
|CachingPoliciesModificationKind      | 取代                                                                                                                                                                         |
