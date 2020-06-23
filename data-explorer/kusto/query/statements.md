---
title: 查詢語句-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的查詢語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 778b8c276d6264b890c0b0be7b127f578663a64d
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85129051"
---
# <a name="query-statement-types"></a>查詢陳述式類型

::: zone pivot="azuredataexplorer"

查詢是由一個或多個**查詢語句**所組成，並以分號（ `;` ）分隔。
這些查詢語句中至少有一個必須是[表格式運算式語句](./tabularexpressionstatements.md)。
表格式運算式語句會產生一或多個表格式結果。
當查詢有一個以上的表格式運算式語句時，查詢會有一[批](./batches.md)表格式運算式語句，而這些語句所產生的表格式結果全都由查詢傳回。

查詢語句的類型有兩種：

* 主要由使用者使用的語句（[使用者查詢語句](#user-query-statements)）、
* 已設計的語句可支援中介層應用程式接受使用者查詢，並將其修改版本傳送至 Kusto （[應用程式查詢語句](#application-query-statements)）的案例。

在這兩種情況下，某些查詢語句很有用。

> [!NOTE]
> 查詢語句的「效果」會從語句出現在查詢中的時間點開始，並在查詢結束時結束。 查詢完成之後，其所有資源都會釋出，而且不會影響未來的查詢（除了副作用，例如在所有查詢的記錄檔中記錄的查詢已執行，或已快取其結果）。

## <a name="user-query-statements"></a>使用者查詢語句

以下是使用者查詢語句的清單：

* [Let 語句](./letstatement.md)會定義名稱與運算式之間的系結。
  Let 語句可用來將長時間查詢分成較容易瞭解的小型命名部分。

* [Set 語句](./setstatement.md)會設定一個查詢選項，它會影響查詢的處理方式和傳回的結果。

* [表格式運算式語句](./tabularexpressionstatements.md)是最重要的查詢語句，會傳回「有趣的」資料做為結果。

## <a name="application-query-statements"></a>應用程式查詢語句

以下是應用程式查詢語句的清單：

* [Alias 語句](./aliasstatement.md)會定義另一個資料庫的別名（在相同的叢集中或在遠端叢集上）。

* [模式語句](./patternstatement.md)，可供以 Kusto 為基礎的應用程式所使用，並向其使用者公開查詢語言，以將自己插入查詢名稱解析程式。

* [查詢參數語句](./queryparametersstatement.md)，由以 Kusto 為基礎的應用程式所使用，以保護自己免于插入式攻擊（類似于命令參數如何保護 sql 以防止 sql 插入式攻擊）。

* [Restrict 語句](./restrictstatement.md)，由 Kusto 之上的應用程式所使用，可將查詢限制為 Kusto 中的特定資料子集（包括限制對特定資料行和記錄的存取權）。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
