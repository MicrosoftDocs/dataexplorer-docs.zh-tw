---
title: 。合併範圍-Azure 資料總管
description: 本文說明 Azure 資料總管中的 [合併範圍] 命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 6b633358713b0ff48d14fc9c5ca2a907bad0afab
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060608"
---
# <a name="merge-extents"></a>。合併範圍

此命令會合並指定的資料表中，其識別碼所指出的範圍。 

> [!NOTE]
> 資料分區在 Kusto 中稱為**範圍**，而所有命令都會使用「範圍」或「範圍」做為同義字。
> 如需有關範圍的詳細資訊，請參閱[範圍（資料分區）總覽](extents-overview.md)。

## <a name="syntax"></a>Syntax

`.merge``[async | dryrun]` *TableName* `(` *GUID1* [ `,` *GUID2* ...] `)``[with(rebuild=true)]`

合併作業有兩種類型：
* 重建索引的*合併*
* 完全 reingests 資料的*重建*

有三個選項可用：
* `async`：以非同步方式執行命令。 
    * 傳回作業識別碼（Guid）。
    * 作業的狀態可以是 [受監視]。 使用作業識別碼搭配 `.show operations <operation ID>` 命令。
* `dryrun`：作業只會列出應該合併的範圍，但不會實際合併它們。
* `with(rebuild=true)`：將會重建範圍，而不會合並資料 reingested。 將會重建索引。

## <a name="return-output"></a>傳回輸出

輸出參數 |類型 |Description
---|---|---
OriginalExtentId |字串 |來源資料表中原始範圍的唯一識別碼（GUID），已合併至目標範圍。
ResultExtentId |字串 |從來源範圍建立之範圍的唯一識別碼（GUID）。 失敗時：「失敗」或「已放棄」。
Duration |時間範圍 |完成合併作業所花費的時間週期。

## <a name="examples"></a>範例

### <a name="rebuild-two-specific-extents-in-mytable-asynchronously"></a>以非同步方式重建中的兩個特定範圍 `MyTable` 。

```kusto
.merge async MyTable (e133f050-a1e2-4dad-8552-1f5cf47cab69, 0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687) with(rebuild=true)
```

### <a name="merge-two-specific-extents-in-mytable-synchronously"></a>以同步方式合併中的兩個特定範圍 `MyTable` 。

```kusto
.merge MyTable (12345050-a1e2-4dad-8552-1f5cf47cab69, 98765b2d-9dd2-4d2c-a45e-b24c65aa6687)
```

## <a name="notes"></a>備註

* 一般情況下， `.merge` 不應手動執行命令。 根據資料表和資料庫的[合併原則](mergepolicy.md)，這些命令會持續自動在叢集的背景中執行。  
  * 如需將多個範圍合併成單一範圍之準則的詳細資訊，請參閱[合併原則](mergepolicy.md)。
* `.merge`作業有可能的最終狀態 `Abandoned` ，當以作業識別碼執行時，就會看到這種情況 `.show operations` 。 此狀態表示來源範圍並未合併，因為某些來源範圍已不存在於來源資料表中。 `Abandoned`狀態發生的時機：
   * 已卸載來源範圍做為資料表保留期的一部分。
   * 來源範圍已移至不同的資料表。
   * 來源資料表已完全卸載或重新命名。
