---
title: 保留策略 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的保留策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 81e08b6e007a6e3c669e7138e1d36ae1e701d442
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520310"
---
# <a name="retention-policy"></a>保留原則

保留策略控制自動從表中刪除數據的機制。
這種刪除通常適用於連續流入相關性基於年齡的表中的數據。 例如,保留策略可用於保存兩周后可能變為未分期的診斷事件的表。

保留策略可以針對特定表或整個資料庫進行配置(在這種情況下,它適用於資料庫中不覆蓋該策略的所有表)。

對於不斷引入數據的群集,設置保留策略非常重要,這將限制成本。

保留策略「外部」的數據有資格刪除。 Kusto 不保證刪除時(因此即使觸發了保留策略,資料也可能"迴流")。

保留策略通常設置為限制自引入後的數據年齡(請參閱[下面的 SoftDelete 週期](#the-policy-object))。

> [!NOTE]
> * 刪除時間不精確。 系統保證在超出限制之前不會刪除數據,但刪除不會立即遵循該點。
> * 軟刪除週期 0 可以設置為表級保留策略的一部分(但不能作為資料庫級保留策略的一部分)。
>   * 完成此操作后,引入的數據將不會提交到源表,從而避免需要保留數據。
>   * 這種配置主要在數據被引入到表中時有用。
>   事務[更新策略](updatepolicy.md)用於轉換它並將輸出重定向到另一個表。

## <a name="the-policy-object"></a>原則物件

保留政策包括以下屬性:

* **軟刪除週期**:
    * 保證數據可供查詢的時間跨度,自引入數據以來進行測量。
    * 預設為 `100 years`。
    * 更改表或資料庫的軟刪除週期時,新值將同時應用於現有數據和新資料。
* **可回復性**:
    * 資料刪除後的資料可復原性(已啟用/關閉)
    * 預設為 `enabled`
    * 如果設定為`enabled`,則資料將在刪除後 14 天內恢復

## <a name="control-commands"></a>控制命令

* 使用[.show 策略保留](../management/retention-policy.md)顯示資料庫或表的當前保留策略。
* 使用[.alter 策略保留](../management/retention-policy.md)更改資料庫或表的當前保留策略。

## <a name="defaults"></a>Defaults

預設情況下,在創建資料庫或表時,它未定義保留策略。
在常見情況下,資料庫被創建,然後立即由其建立者根據已知要求設置其保留策略。
設定未設定的資料庫或表的保留策略執行[show 命令](../management/retention-policy.md)時`Policy`,`null`顯示為 。

可以使用以下指令應用程式預設保留策略 (具有此預設值):

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
```

以下策略物件應用於資料庫或表時,這些結果:

```kusto
{
  "SoftDeletePeriod": "36500.00:00:00", "Recoverability":"Enabled"
}
```

清除資料庫或表的保留原則可以使用以下指令完成:

```kusto
.delete database DatabaseName policy retention
.delete table TableName policy retention
```

## <a name="examples"></a>範例

給定群集具有名為`MyDatabase`的資料庫,`MyTable1``MyTable2`其中有 表 , 和`MySpecialTable`

**1. 將資料庫中的所有表設定為軟刪除週期為 7 天,並關閉可恢復性**:

* *選項 1(建議):* 設定軟刪除週期為 7 天且可恢復性禁用的資料庫級保留策略,並驗證沒有設置表級策略。

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
```

* *選項 2:* 對於每個表,設定一個表級保留策略,軟刪除週期為 7 天,並且可恢復性禁用。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

**2.`MyTable1`設定`MyTable2`表 ,具有 7 天的軟刪除週期並啟用可復`MySpecialTable`原性,並設定為 軟刪除週期為 14 天,並且關閉可復原性**:

* *選項 1(建議):* 設定軟刪除週期為 7 天且啟用可恢復性的資料庫級保留策略,並設置軟刪除週期為 14 天且`MySpecialTable`禁用的可恢復性表 級保留策略。

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *選項 2:* 對於每個表,設定具有所需軟刪除週期和可恢復性的表級保留策略。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

**3.`MyTable1`設定`MyTable2`表 ,具有 7 天的軟刪除`MySpecialTable`週期,並無限期地 保留其資料**:

* *選項 1:* 設定軟刪除週期為 7 天的資料庫級保留策略,並為 設定軟刪除期為 100 年的表級保留策略`MySpecialTable`(預設保留策略)。

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *選項*2`MyTable1``MyTable2`: 對於 表 ,設定所需的軟刪除週期為 7 天的表級保留`MySpecialTable`策略,並驗證未設置的資料庫級和表級策略。

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *選項*3`MyTable1``MyTable2`: 對於 表 ,設定所需的軟刪除週期為 7 天的表級保留策略。 對於表`MySpecialTable`,設定軟刪除週期為 100 年的表級保留策略(預設保留策略)。

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```