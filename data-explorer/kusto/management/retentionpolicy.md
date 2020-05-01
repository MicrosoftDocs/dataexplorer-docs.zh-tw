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
ms.openlocfilehash: 5254f2daee767f51111f2ac3d1be07b7f2bb09f4
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617386"
---
# <a name="retention-policy"></a>保留原則

保留原則會控制從資料表自動移除資料的機制。
這類移除通常適用于流入持續時間為基礎之資料表的資料。 例如，保留原則可以用於保存診斷事件的資料表，這兩周後可能會有意義。

您可以針對特定資料表或整個資料庫設定保留原則（在此情況下，它會套用至資料庫中不會覆寫原則的所有資料表）。

設定保留原則對於持續內嵌資料的叢集非常重要，這會限制成本。

保留原則「外部」的資料符合移除資格。 Kusto 不保證何時會發生移除（因此，即使已觸發保留原則，資料也可能會「延遲」）。

保留原則最常設定為限制自內嵌後的資料存留期（請參閱下面的[SoftDeletePeriod](#the-policy-object)）。

> [!NOTE]
> * 刪除時間不精確。 系統保證在超過限制之前不會刪除資料，但在該時間點之後不會立即刪除。
> * 您可以將虛刪除期間0設定為數據表層級保留原則的一部分（但不是資料庫層級保留原則的一部分）。
>   * 完成此動作時，內嵌資料將不會認可到來源資料表，而不需要保存資料。
>   * 這種設定主要適用于將資料內嵌到資料表時。
>   交易式[更新原則](updatepolicy.md)是用來轉換它，並將輸出重新導向至另一個資料表。

## <a name="the-policy-object"></a>原則物件

保留原則包括下列屬性：

* **SoftDeletePeriod**：
    * 保證資料可供查詢使用的時間範圍，從內嵌的時間開始算起。
    * 預設為 `100 years`。
    * 改變數據表或資料庫的虛刪除期間時，新的值會同時套用至現有和新的資料。
* 復原**能力**：
    * 資料刪除後的資料復原能力（已啟用/已停用）
    * 預設為 `enabled`
    * 如果設定為`enabled`，資料將在刪除後的14天內復原

## <a name="control-commands"></a>控制命令

* 使用[。顯示原則保留](../management/retention-policy.md)以顯示資料庫或資料表的目前保留原則。
* 使用[. alter policy 保留](../management/retention-policy.md)來變更資料庫或資料表的目前保留原則。

## <a name="defaults"></a>Defaults

根據預設，建立資料庫或資料表時，不會定義保留原則。
在一般情況下，會建立資料庫，然後根據已知的需求，立即由其建立者設定其保留原則。
針對尚未設定其原則的資料庫或資料表的保留原則執行[show 命令](../management/retention-policy.md)時， `Policy`會顯示為`null`。

您可以使用下列命令來套用預設的保留原則（使用上述的預設值）：

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
```

這些結果會將下列原則物件套用至資料庫或資料表：

```kusto
{
  "SoftDeletePeriod": "36500.00:00:00", "Recoverability":"Enabled"
}
```

您可以使用下列命令來清除資料庫或資料表的保留原則：

```kusto
.delete database DatabaseName policy retention
.delete table TableName policy retention
```

## <a name="examples"></a>範例

假設您的叢集具有名為`MyDatabase`的資料庫、 `MyTable1`資料表`MyTable2`和`MySpecialTable`

**1. 將資料庫中的所有資料表設定為具有7天的虛刪除期間，並停用復原能力**：

* *選項1（建議使用）*：設定資料庫層級的保留原則，其中包含七天的虛刪除期間和已停用的恢復功能，並確認未設定任何資料表層級原則。

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
```

* *選項 2*：針對每個資料表，設定資料表層級的保留原則，並在7天的虛刪除期間，並停用恢復功能。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

**2. 設定資料表`MyTable1`， `MyTable2`使其具有7天的虛刪除週期並啟用復原功能，並將`MySpecialTable`設定為在14天內停用虛刪除期間，並使復原能力失效**：

* *選項1（建議使用）*：設定資料庫層級的保留原則，並在7天內執行虛刪除期間並啟用復原功能，並設定資料表層級的保留原則，其中包含14天的虛刪除期間和`MySpecialTable`針對停用的復原能力。

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *選項 2*：針對每個資料表，使用所需的虛刪除週期和復原能力來設定資料表層級的保留原則。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

**3. 設定資料表`MyTable1`， `MyTable2`使其具有7天的虛刪除期間，並無限期`MySpecialTable`地保留其資料**：

* *選項 1*：使用七天的虛刪除期間來設定資料庫層級保留原則，並以100年的虛刪除期間（預設保留原則）設定資料表層級的保留原則`MySpecialTable`。

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *選項 2*：如果是`MyTable1`資料表`MyTable2`，請使用七天所需的虛刪除期間來設定資料表層級的保留原則，並確認`MySpecialTable`未設定的資料庫層級和資料表層級原則。

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *選項 3*：針對資料表`MyTable1`， `MyTable2`設定資料表層級的保留原則，並將所需的虛刪除期間設為七天。 針對 [ `MySpecialTable`資料表]，以100年的虛刪除期間（預設的保留原則）設定資料表層級的保留原則。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```
