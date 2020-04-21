---
title: T-SQL - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 T-SQL。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d262d8b7587296c02a2a31d850919af0931bcde6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523404"
---
# <a name="t-sql"></a>T-SQL

Kusto 服務可以解釋和運行具有某些語言限制的 T-SQL 查詢。
雖然[Kusto 查詢語言](../../query/index.md)是 Kusto 的首選語言,但此類支援對於無法輕鬆轉換為使用首選查詢語言的現有工具以及熟悉 SQL 的使用者隨意使用 Kusto 非常有用。

> [!NOTE]
> Kusto 不支援 DDL 命令,僅支援 T-SQL`SELECT`語句。 有關 SQL Server 與 Kusto 在 T-SQL 的內容的詳細資訊,請參閱[SQL 已知問題](./sqlknownissues.md)。

## <a name="querying-kusto-from-kustoexplorer-with-t-sql"></a>使用 T-SQL 從 Kusto.Explorer 查詢庫斯特

Kusto.Explorer 工具支援向 Kusto 發送 T-SQL 查詢。
要指示 Kusto.Explorer 在此模式下執行查詢,請為查詢準備一個空的 T-SQL 註釋行。 例如：

```sql
--
select * from StormEvents
```

## <a name="from-t-sql-to-kusto-query-language"></a>從 T-SQL 到 Kusto 查詢語言

Kusto 支援將 T-SQL 查詢轉換為 Kusto 查詢語言。 例如,熟悉 SQL 的人員可以使用此方法,他們希望更好地瞭解 Kusto 查詢語言。 要將等效的 Kusto 查詢語言返回`SELECT`到某些 T-SQL`EXPLAIN`語句,只需在查詢之前添加即可。

例如,以下 T-SQL 查詢:

```sql
--
explain
select top(10) *
from StormEvents
order by DamageProperty desc
```

產生此輸出:

```kusto
StormEvents
| sort by DamageProperty desc nulls first
| take 10
```