---
title: 查詢語句-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的查詢語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: e546f71c3eb4eda09ff7061dd1a3be9eb6ad6c88
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252879"
---
# <a name="query-statement-types"></a>查詢陳述式類型

::: zone pivot="azuredataexplorer"

查詢包含一或多個以分號分隔的 **查詢語句** (`;`) 。
其中至少有一個查詢語句必須是 [表格式運算式語句](./tabularexpressionstatements.md)。
表格式運算式語句會產生一或多個表格式結果。
當查詢有一個以上的表格式運算式語句時，查詢會有一個表格式運算式語句的 [批次](./batches.md) ，而且這些語句所產生的表格式結果全都會由查詢傳回。

查詢語句有兩種類型：

* 使用者 ([使用者查詢語句](#user-query-statements) 主要使用的語句) ，
* 設計用來支援案例的語句，中介層應用程式會採用使用者查詢，並將修改過的版本傳送至 Kusto ([應用程式查詢語句](#application-query-statements)) 。

在這兩種情況下，有些查詢語句很有用。

> [!NOTE]
> 查詢語句的「效果」會從語句出現在查詢中的位置開始，並在查詢結束時結束。 當查詢完成時，它的所有資源都會釋出，而不會影響後續的查詢 (除了副作用之外，例如讓查詢在所有查詢執行的記錄檔中記錄，或將其結果快取。 ) 

## <a name="user-query-statements"></a>使用者查詢語句

以下是使用者查詢語句的清單：

* [Let 語句](./letstatement.md)會定義名稱和運算式之間的系結。
  Let 語句可以用來將長查詢分成較容易瞭解的小型命名部分。

* [Set 語句](./setstatement.md)會設定查詢選項，此選項會影響查詢的處理方式和傳回的結果。

* [表格式運算式語句](./tabularexpressionstatements.md)是最重要的查詢語句，會傳回「有趣」的資料作為結果。

## <a name="application-query-statements"></a>應用程式查詢語句

以下是應用程式查詢語句的清單：

* [Alias 語句](./aliasstatement.md)會定義相同叢集中的另一個資料庫 (或遠端叢集) 的別名。

* [模式語句](./patternstatement.md)，可供建立在 Kusto 之上的應用程式使用，並將查詢語言公開給其使用者，以將其插入至查詢名稱解析程式。

* 以 Kusto 為基礎的應用程式所使用的 [查詢參數語句](./queryparametersstatement.md)，可保護其本身免于插入式攻擊 (類似于命令參數如何保護 sql 以防止 sql 插入式攻擊。 ) 

* 以 Kusto 為基礎的應用程式所使用的 [restrict 語句](./restrictstatement.md)，可將查詢限制為 Kusto (中的特定資料子集，包括限制特定資料行和記錄的存取權 ) 

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
