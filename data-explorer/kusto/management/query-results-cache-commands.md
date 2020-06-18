---
title: 查詢結果快取-Azure 資料總管
description: 本文說明 Azure 資料總管中的查詢結果快取。
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 630266fdaaaac51037a99a6a5243db58a232ae48
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943059"
---
# <a name="query-results-cache"></a>查詢結果快取

查詢結果快取是專門用來儲存查詢結果的快取。 如需詳細資訊，請參閱[查詢結果](../query/query-results-cache.md)快取。

**查詢結果快取命令**

Kusto 提供兩個命令來進行快取管理和可檢視性：

* [`Show cache`](show-query-results-cache-command.md)：使用此命令來顯示結果快取所公開的統計資料。

* [`Clear cache(rhs:string)`](clear-query-results-cache-command.md)：使用此命令可清除快取的結果。
