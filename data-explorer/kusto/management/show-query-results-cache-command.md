---
title: 顯示查詢結果快取-Azure 資料總管
description: 本文說明。在 Azure 資料總管中顯示查詢結果快取。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 7b7a96a01a4ec2b6c84609b2f9c518637d174390
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349885"
---
# <a name="show-database-cache-query_results"></a>。顯示資料庫快取 query_results

傳回資料表，其中顯示與針對內容資料庫所做之[查詢結果](../query/query-results-cache.md)快取相關的統計資料。

**語法**

`.show database query results cache`

**輸出**
 
|輸出參數 |類型 |描述 
|---|---|---
|NodeId|`string`|叢集節點的識別碼。
|點擊  |`long`|快取點擊次數。
|遺漏  |`long`|快取遺漏的數目。
|CacheCapacityInBytes |`long` |快取容量（以位元組為單位）。
|UsedBytes  |`long` |快取已使用空間。
|計數  |`long`| 儲存在快取中的唯一查詢結果數目。
