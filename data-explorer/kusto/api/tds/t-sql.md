---
title: T-sql-Azure 資料總管
description: 本文說明 Azure 資料總管中的 T-sql。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1115414358a1025d4931484b81d6eda76109e6cd
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373520"
---
# <a name="t-sql-support"></a>T-sql 支援

[Kusto 查詢語言（KQL）](../../query/index.md)是慣用的查詢語言。
不過，t-sql 支援對於無法輕鬆轉換為使用 KQL 的工具很有用。  
T-sql 支援也適用于熟悉 SQL 的人。

Kusto 可以解讀及執行具有某些語言限制的 T-SQL 查詢。

> [!NOTE]
> Kusto 不支援 DDL 命令。 僅 `select` 支援 t-sql 語句。 如需 T-sql 相關主要差異的詳細資訊，請參閱[SQL 已知問題](./sqlknownissues.md)。

## <a name="querying-from-kustoexplorer-with-t-sql"></a>使用 T-sql 從 Kusto 查詢

Kusto 工具支援 T-SQL 查詢以進行 Kusto。
若要指示 Kusto 執行查詢，請使用空白的 T-sql 批註行（）開始查詢 `--` 。 例如：

```sql
--
select * from StormEvents
```

## <a name="from-t-sql-to-kusto-query-language"></a>從 T-sql 到 Kusto 查詢語言

Kusto 支援將 T-SQL 查詢轉譯為 Kusto 查詢語言（KQL）。 這種翻譯可以協助熟悉 SQL 的人員，進一步瞭解 KQL。
若要從某個 T-sql 語句取回對等的 KQL `select` ，請在 `explain` 查詢之前加入。

例如，下列 T-SQL 查詢：

```sql
--
explain
select top(10) *
from StormEvents
order by DamageProperty desc
```

產生下列輸出：

```kusto
StormEvents
| sort by DamageProperty desc nulls first
| take 10
```
