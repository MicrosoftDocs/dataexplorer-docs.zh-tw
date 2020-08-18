---
title: 查詢結果快取命令-Azure 資料總管
description: 本文說明 Azure 資料總管中的查詢結果快取。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 519560f0b6bb98651dc4a8b6749935ec9843eb9c
ms.sourcegitcommit: 5e903c61e779f7bf62f745f13a6038ce2a32e934
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88512561"
---
# <a name="query-results-cache-commands"></a>查詢結果快取命令

查詢結果快取是專用於儲存查詢結果的快取。 如需詳細資訊，請參閱 [查詢結果](../query/query-results-cache.md)快取。

**查詢結果快取命令**

Kusto 提供兩個快取管理和可檢視性命令：

* [`Show cache`](show-query-results-cache-command.md)：使用此命令來顯示結果快取所公開的統計資料。

* [`Clear cache(rhs:string)`](clear-query-results-cache-command.md)：使用這個命令可清除快取的結果。
