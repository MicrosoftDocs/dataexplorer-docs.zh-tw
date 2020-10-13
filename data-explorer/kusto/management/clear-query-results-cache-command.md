---
title: 清除查詢結果快取-Azure 資料總管
description: 瞭解如何在 Azure 資料總管中清除快取的查詢結果。 瞭解要使用哪個命令並查看範例。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 72678453211ada2a6366b771153eeb11a717d7a3
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002938"
---
# <a name="clear-query-results-cache"></a>清除查詢結果快取

清除針對內容資料庫所做的所有快取 [查詢結果](../query/query-results-cache.md) 。

**語法**

`.clear` `database` `cache` `query_results`

**傳回**

此命令會傳回具有下列資料行的資料表：

|資料行    |類型    |描述
|---|---|---
|NodeId|`string`|叢集節點的識別碼。
|Count|`long`|節點刪除的專案數目。

**範例**

```kusto
.clear database cache queryresults
```

|NodeId|項目|
|---|---|
|Node1|42
|Node2|0
