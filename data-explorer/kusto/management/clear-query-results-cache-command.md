---
title: 清除查詢結果快取-Azure 資料總管
description: 本文說明在 Azure 資料總管中清除快取資料庫架構的管理命令。
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 68d6493176696f0241467303f166b8c7160859b7
ms.sourcegitcommit: cf1da667be12656a8c4727c23144421b5a4b1099
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/21/2020
ms.locfileid: "86565444"
---
# <a name="clear-query-results-cache"></a>清除查詢結果快取

清除針對內容資料庫所做的所有快取[查詢結果](../query/query-results-cache.md)。

**語法**

`.clear` `database` `cache` `query_results`

**傳回**

此命令會傳回具有下列資料行的資料表：

|資料行    |類型    |描述
|---|---|---
|NodeId|`string`|叢集節點的識別碼。
|計數|`long`|節點所刪除的專案數。

**範例**

```kusto
.clear database cache queryresults
```

|NodeId|項目|
|---|---|
|Node1|42
|Node2|0
