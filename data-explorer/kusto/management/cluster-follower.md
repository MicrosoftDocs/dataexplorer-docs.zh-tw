---
title: 叢集的對等命令-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的叢集對向命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 73b65cc59ff98bc658c542d690812ccf5ad3895a
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108451"
---
# <a name="cluster-follower-commands"></a>叢集的對等命令

以下列出用於管理「進行中」叢集設定的控制命令。 這些命令會以同步方式執行，但會在下一個週期性架構重新整理時套用。 因此，在套用新的設定之前，可能會有幾分鐘的延遲。

[進行中] 命令包括[資料庫層級命令](#database-level-commands)和[資料表層級命令](#table-level-commands)。

## <a name="database-level-commands"></a>資料庫層級命令

### <a name="show-follower-database"></a>。顯示資料後資料庫

顯示已設定一個或多個資料庫層級覆寫之其他領導者叢集的資料庫（或資料庫）。


**語法**

`.show``follower` *DatabaseName* DatabaseName `database`

`.show``follower` * *DatabaseName1 ... `databases` `(` `,``,` *DatabaseNameN*`)`

**輸出** 

| 輸出參數                     | 類型    | 描述                                                                                                        |
|--------------------------------------|---------|--------------------------------------------------------------------------------------------------------------------|
| DatabaseName                         | String  | 要遵循的資料庫名稱。                                                                           |
| LeaderClusterMetadataPath            | String  | 領導者叢集中繼資料容器的路徑。                                                               |
| CachingPolicyOverride                | String  | 資料庫的覆寫快取原則，序列化為 JSON 或 null。                                         |
| AuthorizedPrincipalsOverride         | String  | 資料庫的已授權主體的覆寫集合，序列化為 JSON 或 null。                    |
| AuthorizedPrincipalsModificationKind | String  | 要使用 AuthorizedPrincipalsOverride （`none`、 `union`或`replace`）套用的修改類型。                  |
| CachingPoliciesModificationKind      | String  | 要使用資料庫或資料表層級快取原則覆寫（`none`、 `union`或`replace`）套用的修改類型。 |
| IsAutoPrefetchEnabled                | Boolean | 是否在每次架構重新整理時預先提取新的資料。        |
| TableMetadataOverrides               | String  | 資料表層級屬性覆寫的 JSON 序列化（如果已定義的話）。                                      |

### <a name="alter-follower-database-policy-caching"></a>。 alter 的資料庫原則快取

改變「後向資料庫」快取原則，以覆寫領導者叢集中源資料庫上的一個設定。 它需要[DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。



**注意事項**

* 快取`modification kind`原則的預設值`union`是。 若要變更`modification kind` ，請使用[alter get-help database caching-原則-修改種類](#alter-follower-database-caching-policies-modification-kind)命令。
* 在變更之後，您可以使用下列`.show`命令來查看原則或有效原則：
    * [。顯示資料庫原則保留期](../management/retention-policy.md#show-retention-policy)
    * [。顯示資料庫詳細資料](../management/show-databases.md)
    * [.show 資料表詳細資料](show-tables-command.md)
* 在進行變更之後，您可以使用來查看執行後資料庫的覆寫設定[。顯示](#show-follower-database)後的資料庫

**語法**

`.alter``follower` `=` *HotDataSpan* *DatabaseName* DatabaseName HotDataSpan `policy` `caching` `database` `hot`



**範例**



```kusto
.alter follower database MyDb policy caching hot = 7d
```

### <a name="delete-follower-database-policy-caching"></a>。刪除進行中的資料庫原則快取

刪除進行中的資料庫覆寫快取原則。 這會使領導者叢集中的源資料庫上所設定的原則成為有效的。
它需要[DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。 

**注意事項**

* 在變更之後，您可以使用下列`.show`命令來查看原則或有效原則：
    * [。顯示資料庫原則保留期](../management/retention-policy.md#show-retention-policy)
    * [。顯示資料庫詳細資料](../management/show-databases.md)
    * [.show 資料表詳細資料](show-tables-command.md)
* 使用來完成變更之後，請在資料後資料庫上查看覆寫設定[。顯示](#show-follower-database)執行後資料庫

**語法**

`.delete``follower` `policy` *DatabaseName* DatabaseName `database``caching`

**範例**

```kusto
.delete follower database MyDB policy caching
```

### <a name="add-follower-database-principals"></a>。加入資料的資料庫主體

將已授權的主體加入至覆寫已授權主體的「後向資料庫」集合。 它需要[DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。



**注意事項**

* 這類`modification kind`授權主體的預設值`none`是。 若要變更`modification kind` ，請使用[alter get-help 資料庫主體-修改種類](#alter-follower-database-principals-modification-kind)。
* 在變更之後，您可以使用`.show`命令來查看有效的主體集合：
    * [。顯示資料庫主體](../management/security-roles.md#managing-database-security-roles)
    * [。顯示資料庫詳細資料](../management/show-databases.md)
* 使用來完成變更之後，請在資料後資料庫上查看覆寫設定[。顯示](#show-follower-database)執行後資料庫

**語法**

`.add``follower` `users` *DatabaseName* `(` * *DatabaseName （`admins``,`）角色 | principal1 ...`viewers`  |  `database`  |  `monitors``,` *principalN* principalN`)` [`'`*notes*notes`'`]




**範例**

```kusto
.add follower database MyDB viewers ('aadgroup=mygroup@microsoft.com') 'My Group'
```

```kusto

```

### <a name="drop-follower-database-principals"></a>。卸載資料的資料庫主體

從覆寫已授權主體的「後向資料庫」集合中卸載授權主體。
它需要[DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。

**注意事項**

* 在變更之後，您可以使用`.show`命令來查看有效的主體集合：
    * [。顯示資料庫主體](../management/security-roles.md#managing-database-security-roles)
    * [。顯示資料庫詳細資料](../management/show-databases.md)
* 使用來完成變更之後，請在資料後資料庫上查看覆寫設定[。顯示](#show-follower-database)執行後資料庫

**語法**

`.drop``follower` `monitors` *DatabaseName* `(` * *DatabaseName （`admins` | `,`） principal1 | ...`users`  |  `database` `viewers``,` *principalN*`)`

**範例**

```kusto
.drop follower database MyDB viewers ('aadgroup=mygroup@microsoft.com')
```

### <a name="alter-follower-database-principals-modification-kind"></a>。 alter get-help 資料庫主體-修改種類

改變「後向資料庫」授權主體修改種類。 它需要[DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。

**注意事項**

* 在變更之後，您可以使用`.show`命令來查看有效的主體集合：
    * [。顯示資料庫主體](../management/security-roles.md#managing-database-security-roles)
    * [。顯示資料庫詳細資料](../management/show-databases.md)
* 使用來完成變更之後，請在資料後資料庫上查看覆寫設定[。顯示](#show-follower-database)執行後資料庫

**語法**

`.alter``follower`  |  *DatabaseName*  | DatabaseName`replace`= （`none`） `database` 
 `principals-modification-kind` `union`



**範例**

```kusto
.alter follower database MyDB principals-modification-kind = union
```

### <a name="alter-follower-database-caching-policies-modification-kind"></a>。 alter get-help 資料庫快取-原則-修改種類

改變「後向資料庫」和「資料表快取原則」修改種類。 它需要[DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。

**注意事項**

* 在變更之後，您可以使用標準`.show`命令來查看資料庫/資料表層級快取原則的有效集合：
    * [。顯示資料表詳細資料](show-tables-command.md)
    * [。顯示資料庫詳細資料](../management/show-databases.md)
* 使用來完成變更之後，請在資料後資料庫上查看覆寫設定[。顯示](#show-follower-database)執行後資料庫

**語法**

`.alter``follower` `union` *DatabaseName* `replace`DatabaseName = （）`none`  |  `database` `caching-policies-modification-kind`  | 



**範例**

```kusto
.alter follower database MyDB caching-policies-modification-kind = union
```



## <a name="table-level-commands"></a>資料表層級命令

### <a name="alter-follower-table-policy-caching"></a>。 alter 的資料表原則快取

改變後端資料庫上的資料表層級快取原則，以覆寫領導者叢集中源資料庫上所設定的原則。
它需要[DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。 



**注意事項**

* 在變更之後，您可以使用下列`.show`命令來查看原則或有效原則：
    * [。顯示資料庫原則保留期](../management/retention-policy.md#show-retention-policy)
    * [。顯示資料庫詳細資料](../management/show-databases.md)
    * [.show 資料表詳細資料](show-tables-command.md)
* 使用來完成變更之後，請在資料後資料庫上查看覆寫設定[。顯示](#show-follower-database)執行後資料庫

**語法**





`.alter``follower` `=` *HotDataSpan* *DatabaseName* `caching` *TableName* DatabaseName 資料表 TableName `policy` HotDataSpan `database` `hot`

`.alter``follower` `(` *DatabaseName* `,`DatabaseName 資料表*TableName1*... `database``,` *TableNameN* TableNameN`)` *HotDataSpan* HotDataSpan `policy` `caching` `hot` `=`

**範例**



```kusto
.alter follower database MyDb tables (Table1, Table2) policy caching hot = 7d
```

### <a name="delete-follower-table-policy-caching"></a>。刪除進行中的資料表原則快取

在後端資料庫上刪除覆寫資料表層級快取原則，讓領導者叢集中的源資料庫上的原則設定成為有效的。 需要[DatabaseAdmin 許可權](../management/access-control/role-based-authorization.md)。 

**注意事項**

* 在變更之後，您可以使用下列`.show`命令來查看原則或有效原則：
    * [。顯示資料庫原則保留期](../management/retention-policy.md#show-retention-policy)
    * [。顯示資料庫詳細資料](../management/show-databases.md)
    * [.show 資料表詳細資料](show-tables-command.md)
* 使用來完成變更之後，請在資料後資料庫上查看覆寫設定[。顯示](#show-follower-database)執行後資料庫

**語法**

`.delete``follower` `table` *TableName* *DatabaseName* DatabaseName TableName `database` `policy``caching`

`.delete``follower` `(` *TableName1* *DatabaseName* DatabaseName `tables` ... `database` `,``,` `)` *TableNameN* TableNameN `policy``caching`

**範例**

```kusto
.delete follower database MyDB tables (Table1, Table2) policy caching
```

## <a name="sample-configuration"></a>範例組態

以下是設定資料後資料庫的範例步驟。

在此範例中：

* 我們的執行者`MyFollowerCluster`叢集將會遵循`MyDatabase`領導者叢集的資料庫`MyLeaderCluster`。
    * `MyDatabase`具有`N`資料表： `MyTable1`、 `MyTable2`、 `MyTable3`、.。。`MyTableN` （`N` > 3）。
    * 在 `MyLeaderCluster`上：
    
    | `MyTable1`快取原則 | `MyTable2`快取原則 | `MyTable3`...`MyTableN`快取原則   | `MyDatabase`授權主體                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | 熱門資料範圍 =`7d`      | 熱門資料範圍 =`30d`     | 熱門資料範圍 =`365d`                   | *Viewers*  = 查看`aadgroup=scubadivers@contoso.com`器;*管理員* = `aaduser=jack@contoso.com` |
     
    * 在`MyFollowerCluster`我們想要：
    
    | `MyTable1`快取原則 | `MyTable2`快取原則 | `MyTable3`...`MyTableN`快取原則   | `MyDatabase`授權主體                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | 熱門資料範圍 =`1d`      | 熱門資料範圍 =`3d`      | 經常性存取資料範圍`0d` = （不快取任何內容） | *Admins*  = 管理員`aaduser=jack@contoso.com`、*查看*器 = `aaduser=jill@contoso.com`         |

> [!IMPORTANT] 
> 和`MyFollowerCluster` `MyLeaderCluster`都必須位於相同的區域。

### <a name="steps-to-execute"></a>執行的步驟

必要條件 *：* 設定叢集`MyFollowerCluster`以遵循叢集`MyDatabase`的資料庫`MyLeaderCluster`。

> [!NOTE]
> 執行控制命令的主體應該是資料庫`DatabaseAdmin` `MyDatabase`上的。

#### <a name="show-the-current-configuration"></a>顯示目前的設定

根據`MyDatabase` `MyFollowerCluster`下列內容，查看目前的設定：

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
|AuthorizedPrincipalsModificationKind | None                                                     |
|IsAutoPrefetchEnabled                | False                                                    |
|TableMetadataOverrides               |                                                          |
|CachingPoliciesModificationKind      | Union                                                    |                                                                                                                      |

#### <a name="override-authorized-principals"></a>覆寫授權的主體

以僅包含一個 AAD 使用者做`MyDatabase`為`MyFollowerCluster`資料庫管理員的集合取代上授權主體的集合，並以一個 aad 使用者作為資料庫檢視器：

```kusto
.add follower database MyDatabase admins ('aaduser=jack@contoso.com')

.add follower database MyDatabase viewers ('aaduser=jill@contoso.com')

.alter follower database MyDatabase principals-modification-kind = replace
```

只有這兩個特定主體已獲授權`MyDatabase`可存取`MyFollowerCluster`

```kusto
.show database MyDatabase principals
```

| [角色]                       | PrincipalType | PrincipalDisplayName                        | PrincipalObjectId                    | PrincipalFQN                                                                      | 注意 |
|----------------------------|---------------|---------------------------------------------|--------------------------------------|-----------------------------------------------------------------------------------|-------|
| 資料庫 MyDatabase 管理員  | AAD 使用者      | 插座 Kusto （upn： jack@contoso.com）       | 12345678-abcd-efef-1234-350bf486087b | 以 aaduser name> = 87654321-abcd-efef-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |       |
| 資料庫 MyDatabase 檢視器 | AAD 使用者      | Jill Kusto （upn： jack@contoso.com）       | abcdefab-abcd-efef-1234-350bf486087b | 以 aaduser name> = 54321789-abcd-efef-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |       |

```kusto
.show follower database MyDatabase
| mv-expand parse_json(AuthorizedPrincipalsOverride)
| project AuthorizedPrincipalsOverride.Principal.FullyQualifiedName
```

| AuthorizedPrincipalsOverride_Principal_FullyQualifiedName                         |
|-----------------------------------------------------------------------------------|
| 以 aaduser name> = 87654321-abcd-efef-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |
| 以 aaduser name> = 54321789-abcd-efef-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |

#### <a name="override-caching-policies"></a>覆寫快取原則

藉由將所有資料表設定為不快取其資料`MyDatabase` ， `MyFollowerCluster`而*不*包含兩個特定資料表（ `MyTable1` `MyTable2` ），將資料快取于`1d`和`3d`的期間，以取代上的資料庫和資料表層級快取原則的集合：

```kusto
.alter follower database MyDatabase policy caching hot = 0d

.alter follower database MyDatabase table MyTable1 policy caching hot = 1d

.alter follower database MyDatabase table MyTable2 policy caching hot = 3d

.alter follower database MyDatabase caching-policies-modification-kind = replace
```

只有這兩個特定的資料表才會快取資料，而其餘的資料表則具有的熱`0d`資料期間：

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
| {"MyTable1"： {"CachingPolicyOverride"： {"DataHotSpan"： {"Value"： "1.00：00： 00"}，"IndexHotSpan"： {"Value"： "1.00：00： 00"}}}} |
| {"MyTable2"： {"CachingPolicyOverride"： {"DataHotSpan"： {"Value"： "3.00：00： 00"}，"IndexHotSpan"： {"Value"： "3.00：00： 00"}}}} |

#### <a name="summary"></a>摘要

查看目前的設定， `MyDatabase`其中會遵循`MyFollowerCluster`：

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| 資料行                              | 值                                                                                                                                                                           |
|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|DatabaseName                         | MyDatabase                                                                                                                                                                      |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.windows.net/cluster`                                                                                                                        |
|CachingPolicyOverride                | {"DataHotSpan"： {"Value"： "00：00： 00"}，"IndexHotSpan"： {"Value"： "00：00： 00"}}                                                                                                        |
|AuthorizedPrincipalsOverride         | [{"Principal"： {"FullyQualifiedName"： "以 aaduser name> = 87654321-abcd-efef-1234-350bf486087b",...}，{"Principal"： {"FullyQualifiedName"： "以 aaduser name> = 54321789-abcd-efef-1234-350bf486087b",...}] |
|AuthorizedPrincipalsModificationKind | Replace                                                                                                                                                                         |
|IsAutoPrefetchEnabled                | False                                                                                                                                                                           |
|TableMetadataOverrides               | {"MyTargetTable"： {"CachingPolicyOverride"： {"DataHotSpan"： {"Value"： "3.00：00： 00"} ...}，"MySourceTable"： {"CachingPolicyOverride"： {"DataHotSpan"： {"Value"： "1.00：00： 00"},...}}}       |
|CachingPoliciesModificationKind      | Replace                                                                                                                                                                         |