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
ms.openlocfilehash: 9ddb94e005e88855bbeddd7d3cf8ab537a42d413
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943060"
---
# <a name="clear-query-results-cache"></a>清除查詢結果快取

清除針對內容資料庫所做的所有快取[查詢結果](../query/query-results-cache.md)。

**語法**

`.clear` `database` `cache` `queryresults`

**傳回**

此命令會傳回具有下列資料行的資料表：

|資料行    |類型    |描述
|---|---|---
|NodeId|`string`|叢集節點的識別碼。
|項目|`long`|節點清除的專案數。

**範例**

```kusto
.clear database cache queryresults
```

|NodeId|項目|
|---|---|
|Node1|42
|Node2|0
