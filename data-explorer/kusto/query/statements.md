---
title: 查詢敘述 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的查詢語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 20eeb1aa755fcf4e3068cba061a2738a375e1847
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765553"
---
# <a name="query-statements"></a>查詢陳述式

::: zone pivot="azuredataexplorer"

查詢由一個或多個**查詢語句**組成,由分號`;`() 分隔。
這些查詢敘述中至少有一個必須是[表格表示式語句](./tabularexpressionstatements.md)。
表格運算式語句生成一個或多個表格結果。
當查詢具有多個表格表達式語句時,查詢具有一[批](./batches.md)表格表達式語句,並且這些語句生成的表格結果都由查詢返回。

兩種類型的查詢語句:

* 主要由使用者使用的語句 ([用戶查詢語句](#user-query-statements)),
* 旨在支援中端應用程式獲取使用者查詢並將修改後的版本發送到 Kusto([應用程式查詢敘述](#application-query-statements))的方案的語句。

某些查詢語句在這兩種情況下都很有用。

> [!NOTE]
> 查詢語句的「效果」從該語句出現在查詢中並在查詢末尾結束的點開始。 查詢完成後,將釋放其所有資源,並且對將來的查詢沒有影響(副作用,例如在所有查詢的日誌中記錄查詢或緩存其結果)。

## <a name="user-query-statements"></a>使用者查詢敘述

以下是使用者查詢敘述清單:

* [let 語句](./letstatement.md)定義名稱和表達式之間的綁定。
  let 語句可用於將長查詢分解為更易於理解的小型命名部件。

* [set 語句](./setstatement.md)設置一個查詢選項,該選項會影響如何處理查詢及其返回結果。

* [表格表示式語句](./tabularexpressionstatements.md)(最重要的查詢語句)返回「有趣」數據作為結果返回。

## <a name="application-query-statements"></a>應用程式查詢敘述

以下是應用程式查詢敘述清單:

* [別名語句](./aliasstatement.md)將別名定義為另一個資料庫(在同一群集或遠端群集上)。

* [模式語句](./patternstatement.md),它可用於構建在 Kusto 之上的應用程式,並將查詢語言公開給使用者以注入查詢名稱解析過程。

* [查詢參數語句](./queryparametersstatement.md),由構建在 Kusto 之上的應用程式使用,以保護自己免受注入攻擊(類似於命令參數如何保護 SQL 免受 SQL 注入攻擊)。

* [限制敘述](./restrictstatement.md),由建立在 Kusto 之上的應用程式使用,用於將查詢限制到 Kusto 中的特定資料子集(包括限制對特定列和記錄的存取)。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援此功能

::: zone-end
