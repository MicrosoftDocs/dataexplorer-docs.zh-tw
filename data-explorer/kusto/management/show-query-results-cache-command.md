---
title: 顯示查詢結果快取-Azure 資料總管
description: 本文說明。在 Azure 資料總管中顯示查詢結果快取。
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: bcbdb59355ce0461d735cbea902551c219479fd2
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943056"
---
# <a name="show-query-results-cache"></a>。顯示查詢結果快取

傳回資料表，其中顯示與[查詢結果](../query/query-results-cache.md)快取相關的統計資料。

**語法**

`.show` `query` `results` `cache`

**輸出**
 
|輸出參數 |類型 |描述 
|---|---|---
|點擊  |long |快取點擊次數。
|遺漏  |long |快取遺漏的數目。
|CacheCapacityInBytes |long |快取容量（以位元組為單位）。
|UsedBytes  |long |快取已使用空間。
|Count  |long | 儲存在快取中的唯一查詢結果數目。

**限制**

* 命令的輸出目前只會反映要求所進入的節點所收集的快取統計資料。
* 此命令只會顯示「最近」的歷程記錄。
