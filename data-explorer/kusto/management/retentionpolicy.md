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
ms.openlocfilehash: 7c9bc193e739011a5f91d9bd5d4d8746a7ce2591
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780553"
---
# <a name="retention-policy"></a>保留原則

保留原則會控制自動從資料表移除資料的機制。 移除持續流入資料表的資料，且其相關性是以存留期為基礎，非常有用。 例如，您可以將此原則用於保存診斷事件的資料表，這兩周後可能會有意義。

您可以針對特定資料表或整個資料庫設定保留原則。
接著，原則會套用至資料庫中不會覆寫它的所有資料表。

設定保留原則對於持續內嵌資料的叢集非常重要，這會限制成本。

保留原則「外部」的資料符合移除資格。 Kusto 不保證會在移除時發生。 即使已觸發保留原則，資料也可能會「延遲」。

保留原則最常設定為限制自內嵌後的資料存留期。
如需詳細資訊，請參閱[SoftDeletePeriod](#the-policy-object)。

> [!NOTE]
> * 刪除時間不精確。 系統保證在超過限制之前不會刪除資料，但在該時間點之後不會立即刪除。
> * 0的虛刪除期間可以設定為數據表層級保留原則的一部分，但不能做為資料庫層級保留原則的一部分。
>   * 完成此作業時，不會將內嵌資料認可到來源資料表，而不需要保存資料。 因此，只能 `Recoverability` 設定為 `Disabled` 。
>   * 這種設定主要適用于將資料內嵌到資料表時。
> 交易式[更新原則](updatepolicy.md)是用來轉換它，並將輸出重新導向至另一個資料表。

## <a name="the-policy-object"></a>原則物件

保留原則包括下列屬性：

* **SoftDeletePeriod**：
    * 保證資料可供查詢的時間範圍。 週期是從內嵌資料的時間開始測量。
    * 預設為 `100 years`。
    * 改變數據表或資料庫的虛刪除期間時，新的值會同時套用至現有和新的資料。
* 復原**能力**：
    * 資料在刪除後的資料復原能力（已啟用/已停用）。
    * 預設為 `Enabled`。
    * 如果設定為 `Enabled` ，資料會在虛刪除之後的14天內復原。

## <a name="control-commands"></a>控制命令

* 使用[。顯示原則保留](../management/retention-policy.md)以顯示資料庫或資料表的目前保留原則。
* 使用[. alter policy 保留](../management/retention-policy.md)來變更資料庫或資料表的目前保留原則。

## <a name="defaults"></a>Defaults

根據預設，建立資料庫或資料表時，不會定義保留原則。 通常會建立資料庫，然後根據已知的需求，立即依據其建立者設定其保留原則。
當您針對未設定其原則的資料庫或資料表的保留原則執行[show 命令](../management/retention-policy.md)時， `Policy` 會顯示為 `null` 。

您可以使用下列命令來套用預設的保留原則，以及上述的預設值。

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
```

此命令會導致下列原則物件套用至資料庫或資料表。

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

針對具有名為之資料庫的叢集， `MyDatabase` 包含資料表 `MyTable1` 、 `MyTable2` 和 `MySpecialTable` 。

### <a name="soft-delete-period-of-seven-days-and-recoverability-disabled"></a>已停用7天的虛刪除期間和復原能力

將資料庫中的所有資料表設定為有七天的虛刪除期間，並停用復原能力。

* *選項1（建議）*：設定資料庫層級的保留原則，並確認未設定任何資料表層級原則。

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
```

* *選項 2*：針對每個資料表，設定資料表層級的保留原則，並在7天內執行虛刪除期間，並停用恢復功能。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

### <a name="soft-delete-period-of-seven-days-and-recoverability-enabled"></a>虛刪除期間已啟用7天和復原能力

* 設定資料表 `MyTable1` ，並 `MyTable2` 啟用7天的虛刪除週期和復原能力。
* 設定 `MySpecialTable` 為有14天的虛刪除期間，且恢復功能已停用。

* *選項1（建議）*：設定資料庫層級的保留原則，以及設定資料表層級的保留原則。

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *選項 2*：針對每個資料表，設定資料表層級的保留原則，以及相關的虛刪除期間和復原能力。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

### <a name="soft-delete-period-of-seven-days-and-myspecialtable-keeps-its-data-indefinitely"></a>虛刪除期間的七天，並 `MySpecialTable` 無限期地保留其資料

設定資料表 `MyTable1` 並 `MyTable2` 讓其具有七天的虛刪除週期，並 `MySpecialTable` 無限期地保留其資料。

* *選項 1*：設定資料庫層級的保留原則，並設定資料表層級的保留原則，其中包含100年的虛刪除期間（的預設保留原則） `MySpecialTable` 。

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

* *選項 3*：針對資料表 `MyTable1` 和 `MyTable2` ，設定資料表層級的保留原則。 針對 [資料表] `MySpecialTable` ，以100年的虛刪除期間（預設保留原則）來設定資料表層級保留原則。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```
