---
title: Kusto 保留原則可控制資料的移除方式-Azure 資料總管
description: 本文說明 Azure 資料總管中的保留原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 871ad751105ba6a3f6ce5dcba55b3a0fd1c17789
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91056980"
---
# <a name="retention-policy"></a>保留原則

保留原則可控制自動從資料表或 [具體化視圖](materialized-views/materialized-view-overview.md)中移除資料的機制。 移除持續流入資料表的資料，以及其相關性以 age 為基礎時，會很有用。 例如，此原則可用於保存診斷事件的資料表，這些事件可能會在兩周後變成不重要。

您可以針對特定資料表或具體化視圖設定保留原則，或針對整個資料庫設定保留原則。 原則接著會套用至資料庫中不會覆寫該原則的所有資料表。

設定保留原則對於持續擷取資料的叢集很重要，這會限制成本。

「外部」保留原則的資料有資格移除。 移除發生時沒有特定的保證。 即使觸發保留原則，資料也可能會「逗留」。

保留原則最常見的設定是在內嵌之後限制資料的存在時間。 如需詳細資訊，請參閱 [SoftDeletePeriod](#the-policy-object)。

> [!NOTE]
> * 刪除時間不精確。 系統保證在超過限制之前不會刪除資料，但不會在該點之後立即刪除。
> * 虛刪除期間0可以設定為數據表層級保留原則的一部分，但不能做為資料庫層級保留原則的一部分。
>   * 完成這項操作時，內嵌資料不會認可至來源資料表，以避免需要保存資料。 因此，只能將 `Recoverability` 設定為 `Disabled` 。
>   * 這種設定在資料內嵌至資料表時，主要很有用。
> 交易式 [更新原則](updatepolicy.md) 可用來轉換它，並將輸出重新導向至另一個資料表。

## <a name="the-policy-object"></a>原則物件

保留原則包含下列屬性：

* **SoftDeletePeriod**：
    * 保證資料保持可供查詢的時間範圍。 這段期間是從資料內嵌的時間開始測量。
    * 預設值為 `100 years`。
    * 改變數據表或資料庫的虛刪除期間時，新值會套用至現有和新的資料。
* 復原**能力**：
    * 資料恢復功能 (在刪除資料後) 啟用/停用。
    * 預設值為 `Enabled`。
    * 如果設定為 `Enabled` ，資料將會在被虛刪除後14天復原。

## <a name="control-commands"></a>控制命令

* 使用 [。 [顯示原則保留](../management/retention-policy.md) ] 可顯示資料庫、資料表或 [具體化視圖](materialized-views/materialized-view-overview.md)目前的保留原則。
* 使用 [. alter policy 保留](../management/retention-policy.md) 來變更資料庫、資料表或 [具體化視圖](materialized-views/materialized-view-overview.md)目前的保留原則。

## <a name="defaults"></a>Defaults

根據預設，建立資料庫或資料表時，不會定義保留原則。 通常會建立資料庫，然後根據已知的需求立即設定其建立者的保留原則。
當您針對尚未設定原則的資料庫或資料表的保留原則執行 [show 命令](../management/retention-policy.md) 時， `Policy` 會顯示為 `null` 。

您可以使用下列命令套用預設的保留原則（具有上述的預設值）。

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
.alter materialized-view ViewName policy retention "{}"
```

此命令會產生下列套用至資料庫或資料表的原則物件。

```kusto
{
  "SoftDeletePeriod": "36500.00:00:00", "Recoverability":"Enabled"
}
```

您可以使用下列命令來清除資料庫或資料表的保留原則。

```kusto
.delete database DatabaseName policy retention
.delete table TableName policy retention
```

## <a name="examples"></a>範例

針對具有名為之資料庫的叢集 `MyDatabase` ，以及資料表 `MyTable1` 、 `MyTable2` 和 `MySpecialTable` 。

### <a name="soft-delete-period-of-seven-days-and-recoverability-disabled"></a>已停用七天的虛刪除期間和復原能力

設定資料庫中的所有資料表，使其具有七天的虛刪除期並停用復原。

* *選項 1 (建議的) *：設定資料庫層級的保留原則，並確認沒有設定資料表層級原則。

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge materialized-view ViewName policy retention softdelete = 7d 
```

* *選項 2*：針對每個資料表，設定資料表層級的保留原則，並在七天內停用虛刪除，並停用恢復功能。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

### <a name="soft-delete-period-of-seven-days-and-recoverability-enabled"></a>在七天內啟用的虛刪除期間和復原能力

* 設定資料表 `MyTable1` ，並 `MyTable2` 在七天內啟用可復原的虛刪除週期。
* 設定 `MySpecialTable` 為有14天的虛刪除期，且恢復功能已停用。

* *選項 1 (建議的) *：設定資料庫層級的保留原則，並設定資料表層級的保留原則。

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *選項 2*：針對每個資料表，使用相關的虛刪除期間和復原能力，設定資料表層級的保留原則。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

### <a name="soft-delete-period-of-seven-days-and-myspecialtable-keeps-its-data-indefinitely"></a>七天的虛刪除期，並 `MySpecialTable` 無限期保留其資料

設定資料表 `MyTable1` 並將 `MyTable2` 虛刪除期間設定為七天，並將 `MySpecialTable` 其資料無限期保留。

* *選項 1*：設定資料庫層級的保留原則，並設定資料表層級的保留原則，其為的虛刪除期限為100年，預設保留原則為 `MySpecialTable` 。

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *選項 2*：針對資料表 `MyTable1` 和 `MyTable2` ，設定資料表層級的保留原則，並確認未設定的資料庫層級和資料表層級原則 `MySpecialTable` 。

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *選項 3*：對於資料表 `MyTable1` 和 `MyTable2` ，請設定資料表層級的保留原則。 若是資料表 `MySpecialTable` ，請使用虛刪除期間的100年（預設保留原則）設定資料表層級的保留原則。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```
