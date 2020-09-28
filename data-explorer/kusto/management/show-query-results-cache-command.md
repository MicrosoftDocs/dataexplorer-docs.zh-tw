---
title: 顯示查詢結果快取-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中顯示查詢結果快取。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: e037b6ed01a2eba7c7370d75a4efea0f7cbc1756
ms.sourcegitcommit: 92b8057a36bd7daa16226f1526b29253bceb3602
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91402713"
---
# <a name="show-database-cache-query_results"></a>。顯示資料庫快取 query_results

傳回資料表，其中顯示與針對內容資料庫所做之 [查詢結果](../query/query-results-cache.md) 快取相關的統計資料。

**語法**

`.show database cache query_results`

**輸出**
 
|輸出參數 |類型 |描述 
|---|---|---
|NodeId|`string`|叢集節點的識別碼。
|點擊  |`long`|快取點擊次數。
|遺漏  |`long`|快取遺漏的數目。
|CacheCapacityInBytes |`long` |快取容量（以位元組為單位）。
|UsedBytes  |`long` |快取已使用的空間。
|計數  |`long`| 儲存在快取中的唯一查詢結果數目。
